---
layout: article
title: Collection - Java Web App Notes
key: c01-java-webapp-notes
categories: Collections
tags: Tomcat, WebApp
---

## Anti Cross-Site Scripting (XSS)

Here is a good and simple anti cross-site scripting (XSS) filter written for Java web applications. What it basically does is remove all suspicious strings from request parameters before returning them to the application.

You should configure it as the first filter in your chain (web.xml) and it’s generally a good idea to let it catch every request made to your site.

<!--more-->

```java
public class XSSFilter implements Filter {
	@Override
	public void init(FilterConfig filterConfig) throws ServletException {
	}

	@Override
	public void destroy() {
	}

	@Override
	public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
		chain.doFilter(new XSSRequestWrapper((HttpServletRequest) request), response);
	}
}

public class XSSRequestWrapper extends HttpServletRequestWrapper {
	private static Pattern[] patterns = new Pattern[] {
			// Script fragments
			Pattern.compile("<script>(.*?)</script>", Pattern.CASE_INSENSITIVE),
			// src='...'
			Pattern.compile("src[\r\n]*=[\r\n]*\\\'(.*?)\\\'", Pattern.CASE_INSENSITIVE | Pattern.MULTILINE | Pattern.DOTALL), Pattern.compile("src[\r\n]*=[\r\n]*\\\"(.*?)\\\"", Pattern.CASE_INSENSITIVE | Pattern.MULTILINE | Pattern.DOTALL),
			// lonely script tags
			Pattern.compile("</script>", Pattern.CASE_INSENSITIVE), Pattern.compile("<script(.*?)>", Pattern.CASE_INSENSITIVE | Pattern.MULTILINE | Pattern.DOTALL),
			// eval(...)
			Pattern.compile("eval\\((.*?)\\)", Pattern.CASE_INSENSITIVE | Pattern.MULTILINE | Pattern.DOTALL),
			// expression(...)
			Pattern.compile("expression\\((.*?)\\)", Pattern.CASE_INSENSITIVE | Pattern.MULTILINE | Pattern.DOTALL),
			// javascript:...
			Pattern.compile("javascript:", Pattern.CASE_INSENSITIVE),
			// vbscript:...
			Pattern.compile("vbscript:", Pattern.CASE_INSENSITIVE),
			// onload(...)=...
			Pattern.compile("onload(.*?)=", Pattern.CASE_INSENSITIVE | Pattern.MULTILINE | Pattern.DOTALL) };

	public XSSRequestWrapper(HttpServletRequest servletRequest) {
		super(servletRequest);
	}

	@Override
	public String[] getParameterValues(String parameter) {
		String[] values = super.getParameterValues(parameter);

		if (values == null) {
			return null;
		}

		int count = values.length;
		String[] encodedValues = new String[count];
		for (int i = 0; i < count; i++) {
			encodedValues[i] = stripXSS(values[i]);
		}

		return encodedValues;
	}

	@Override
	public String getParameter(String parameter) {
		String value = super.getParameter(parameter);

		return stripXSS(value);
	}

	@Override
	public String getHeader(String name) {
		String value = super.getHeader(name);
		return stripXSS(value);
	}

	private String stripXSS(String value) {
		if (value != null) {
			// NOTE: It's highly recommended to use the ESAPI library and uncomment the following line to
			// avoid encoded attacks.
			// value = ESAPI.encoder().canonicalize(value);

			// Avoid null characters
			value = value.replaceAll("\0", "");

			// Remove all sections that match a pattern
			for (Pattern scriptPattern : patterns) {
				value = scriptPattern.matcher(value).replaceAll("");
			}
		}
		return value;
	}
```

## HTTP/HTTPS Client Operations

### Upload Data File to Server

Here is a full function (client) to upload a zip file to a server using HTTP, please also note the UTF-8:

```java
	protected void uploadBulkFile(BatchJob job) throws Exception {
		final String CRLF = "\r\n";
		URL url = new URL(job.getUploadUrl());
		HttpURLConnection connection = null;

		FileInputStream reader = null;
		OutputStream output = null;
		PrintWriter writer = null;

		try {
			// Set up the connection and headers
			connection = (HttpURLConnection) url.openConnection(Proxy.NO_PROXY);
			connection.setUseCaches(false);
			connection.setDoOutput(true);
			connection.setDoInput(true);

			// Set up the authentication properties
			AuthorizationData authorizationData = serviceClient.getAuthorizationData();
			PasswordAuthentication passwordAuthentication = (PasswordAuthentication) authorizationData.getAuthentication();
			connection.setRequestProperty("UserName", passwordAuthentication.getUserName());
			connection.setRequestProperty("Password", passwordAuthentication.getPassword());

			// Content-Type must be multipart/form-data with custom boundary
			String boundary = "--------------------" + Long.toString(System.currentTimeMillis(), 16);
			String contentType = "multipart/form-data; boundary=" + boundary;
			connection.setRequestProperty("Content-Type", contentType);

			final int bufferSize = 100 * 1024;
			byte[] buffer = new byte[bufferSize];

			File file = new File(job.getBulkFile());
			reader = new FileInputStream(file);
			output = connection.getOutputStream();
			writer = new PrintWriter(new OutputStreamWriter(output, "UTF-8"), true);

			// Add the file within the specified boundary
			writer.append("--" + boundary).append(CRLF);
			writer.append("Content-Disposition: form-data; name=\"file\"; filename=\"" + file.getName() + "\"").append(CRLF);
			writer.append("Content-Type: application/zip").append(CRLF);
			writer.append("Content-Transfer-Encoding: binary").append(CRLF);
			writer.append(CRLF);
			writer.flush();

			int count = 0;
			while ((count = reader.read(buffer)) != -1) {
				output.write(buffer, 0, count);
			}

			output.flush();

			writer.append(CRLF).flush();
			writer.append("--" + boundary + "--").append(CRLF);
			writer.flush();

			logger.debug("Upload Connection Response: {}", connection.getResponseMessage());
		} finally {
			reader.close();
			writer.flush();
			writer.close();
			output.flush();
			output.close();
		}
	}
```

### Customized Apache Http Client

```java
public class ApiHttpClient {
	private final CloseableHttpClient httpClient;

	public ApiHttpClient(String configSection) {
		HttpClientBuilder builder = HttpClients.custom();
		Registry<ConnectionSocketFactory> socketFactoryRegistry = null;

		// use client cert if configured so
		if (Config.getBool(configSection, "UseClientCert", false)) {
			ClassLoader classLoader = ApiHttpClient.class.getClassLoader();
			File clientCertFile = new File(classLoader.getResource(Config.getStr(configSection, "ClientCertFile")).getFile());
			try {
				KeyStore keystore = KeyStore.getInstance("PKCS12");
				keystore.load(new FileInputStream(clientCertFile), Config.getStr(configSection, "ClientCertPassword").toCharArray());
				SSLContext sslcontext = SSLContexts.custom().loadKeyMaterial(keystore, Config.getStr(configSection, "ClientCertPassword").toCharArray()).build();
				SSLConnectionSocketFactory sslsf = new SSLConnectionSocketFactory(sslcontext.getSocketFactory(), new NoopHostnameVerifier());
				socketFactoryRegistry = RegistryBuilder.<ConnectionSocketFactory>create().register("https", sslsf).register("http", PlainConnectionSocketFactory.getSocketFactory()).build();

			} catch (KeyStoreException | CertificateException | NoSuchAlgorithmException | IOException | UnrecoverableKeyException | KeyManagementException e) {
				throw new RuntimeException(e);
			}
		}

		// use pooling http client manager
		PoolingHttpClientConnectionManager connectionManager = null;
		if (socketFactoryRegistry == null)
			connectionManager = new PoolingHttpClientConnectionManager();
		else
			connectionManager = new PoolingHttpClientConnectionManager(socketFactoryRegistry);
		connectionManager.setMaxTotal(Config.getInt(configSection, "MaxTotal", 200));
		connectionManager.setDefaultMaxPerRoute(Config.getInt(configSection, "DefaultMaxPerRoute", 20));
		builder.setConnectionManager(connectionManager);

		// set host proxy if needed
		if (Config.getBool(configSection, "UseProxy", false)) {
			HttpHost proxy = new HttpHost(Config.getStr(configSection, "ProxyHost"), Config.getInt(configSection, "ProxyPort"));
			builder.setProxy(proxy);
		}

		/*
		 * // add more request settings RequestConfig config =
		 * RequestConfig.custom()
		 * .setConnectTimeout(Config.getInt(configSection, "ConnectionTimeout",
		 * 20000)) .setSocketTimeout(Config.getInt(configSection,
		 * "SocketTimeout", 30000)).build();
		 *
		 * builder.setDefaultRequestConfig(config);
		 */

		this.httpClient = builder.build();
	}

	public <T> T execute(HttpUriRequest request, ResponseHandler<? extends T> responseHandler) throws ClientProtocolException, IOException {
		return httpClient.execute(request, responseHandler);
	}

	public CloseableHttpResponse execute(HttpUriRequest request, HttpContext context) throws IOException, ClientProtocolException {
		return httpClient.execute(request, context);
	}

	public <T> T execute(HttpUriRequest request, ResponseHandler<? extends T> responseHandler, HttpContext context) throws IOException, ClientProtocolException {
		return httpClient.execute(request, responseHandler, context);
	}

	public CloseableHttpResponse execute(HttpUriRequest request) throws IOException, ClientProtocolException {
		return httpClient.execute(request);
	}
}
```

### React

React is a declarative, efficient, and flexible JavaScript library for building user interfaces. It lets you compose complex UIs from small and isolated pieces of code called “components”.

We use components to tell React what we want to see on the screen. When our data changes, React will efficiently update and re-render our components.

```js
class ShoppingList extends React.Component {
  render() {
    return (
      <div className="shopping-list">
        <h1>Shopping List for {this.props.name}</h1>
        <ul>
          <li>Instagram</li>
          <li>WhatsApp</li>
          <li>Oculus</li>
        </ul>
      </div>
    );
  }
}

// Example usage: <ShoppingList name="Mark" />
```


### jQuery Dialog for Rule Settings

```js
function updateRuleSettings(updateButton) {
	$.getJSON("getRule.do?ruleId=" + $(updateButton).closest("tr").attr("id")).done(function(rule) {
		$("#dialogUpdateSettings input[name='id']").val(rule.id);
		$("#dialogUpdateSettings input[name='encodeUrlParams']").prop("checked", rule.ruleSettings['ENCODE_URL_PARAMS'] == 'true');
		$("#dialogUpdateSettings input[name='appendSlingshotID']").prop("checked", rule.ruleSettings['APPEND_SLINGSHOT_ID'] == 'true');
		$("#dialogUpdateSettings select[name='fowardRequestMethod']").val(rule.ruleSettings['FORWARD_REQUEST_METHOD']);
		$("#dialogUpdateSettings input[name='passForwardRestParams']").prop("checked", rule.ruleSettings['PASS_FORWARD_REST_PARAMS']);
		var validateOptions = [];
		if (rule.validateLevel == null || rule.validateLevel == 0) {
			validateOptions.push("0");
		} else {
				if ((rule.validateLevel >> 0 & 1) == 1)
				validateOptions.push("1");  
				if ((rule.validateLevel >> 1 & 1) == 1)
						validateOptions.push("2");
				if ((rule.validateLevel >> 2 & 1) == 1)
						validateOptions.push("4");
				if ((rule.validateLevel >> 3 & 1) == 1)
						validateOptions.push("8");
		}
		$("#dialogUpdateSettings select[name='validateOptions']").val(validateOptions);			
		$("#dialogUpdateSettings").dialog({
			autoOpen : false,
			resizable : false,
			height : 'auto',
			width : 'auto',
			modal : true,
			buttons : {
				Submit: function() {
					var params = $("#dialogUpdateSettings form").serialize();
					$.post("updateSettings.do", params, function(result) {
						if (result == 'OK') {
							$("#slingshotRuleTable").dataTable({bRetrieve : true}).fnDraw(true);
							$("#dialogUpdateSettings").dialog().dialog("close");
						} else {
							$("#dialogUpdateSettings #message").html(result).show();
						}
					});						
				},
				Cancel: function() {
					$(this).dialog("close");
				}
			},
			open: function(event, ui) {

			},
			close: function() {
				$("#dialogUpdateSettings form")[0].reset();
				$("#dialogUpdateSettings #message").html("").hide();
			}
		}).dialog("open");
	})
}
```

## Tomcat Server Troubleshooting

### Cookie value with double quote issue

If Tomcat 7+ starts adding unexpected double quote around the cookie value due to '=' sign presents in value, you can add this line to context.xml:

```
<CookieProcessor className="org.apache.tomcat.util.http.LegacyCookieProcessor" allowHttpSepsInV0="true" />
```

Or uncomment the following line in catalina.properties:

```
org.apache.tomcat.util.http.ServerCookie.ALLOW_HTTP_SEPARATORS_IN_V0=true
```

### Url with invalid character issue

To deal with this error in Tomcat 8+: _java.lang.IllegalArgumentException: Invalid character found in the request target. The valid characters are defined in RFC 7230 and RFC 3986_

You can use following settings and specify some allowed characters in URL:

```
# Allow for changes to HTTP request validation
#WARNING: Using this option will expose the server to CVE-2016-6816
tomcat.util.http.parser.HttpParser.requestTargetAllow=|
```

### Splunk Search Query Samples

```
index=sales sourcetype=vendor_sales
| dedup Vendor  
| sort limit=10 - VendorCountry, +VendorStateProvince, VendorCity, Vendor  
| table VendorCountry, VendorStateProvince, VendorCity, Vendor
```

```
index=web sourcetype=access_combined action=* productId=*
| dedup clientip, productId
| table productId, clientip, action
| sort productId, -clientip
| rename productId as "Product #", clientip as "Client IP Address", action as "Action Taken"
```

```
index=security sourcetype=linux_secure (fail* OR invalid)
| top limit=5 src_ip countfield=ATTACKS showperc=f useother=t
```

```
index=network sourcetype=cisco_wsa_squid
| stats dc(s_hostname) as "Websites visited:" values(s_hostname) as "Site Names"  count as "Visits" list(s_hostname) as "List of visits" by cs_username
```

```
index=web sourcetype=access_combined
| top limit=2 status by host
| fields - count
| sort host, -percent
```

```
index=web sourcetype=access_combined action=*
| stats count, avg(price), sum(price) by action
| rename count as "Total Events", avg(price) as "Average Price", sum(price) as "Total Amount", action as Action
```

```
sourcetype=vendor_sales earliest=-21d@d latest=@d
| timechart sum(price) as Revenue
| timewrap 1w series=exact
```
