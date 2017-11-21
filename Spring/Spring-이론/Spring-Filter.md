

맨처음에 스프링은 필터를 거치게된다, 이걸 사용해서 여러가지를 재밌게 구현할 수 있음,
```java

@Component
public class NotFoundFilter implements Filter {

	private static final Logger logger = LoggerFactory.getLogger(NotFoundFilter.class);

	@Autowired
	Environment env;

	@Override
	public void init(FilterConfig filterConfig) throws ServletException {
	}

	@Override
	public void doFilter(ServletRequest req, ServletResponse res, FilterChain chain)
			throws IOException, ServletException {

		HttpServletRequest request = (HttpServletRequest) req;
		HttpServletResponse response = (HttpServletResponse) res;

		try {
			String uri = request.getRequestURI().toString();
			if (request.getHeader("accept").toString().indexOf("text/html") > -1 && isConvert(uri)) {
				String url = "";
				if (uri.indexOf("/index.html") > -1 || uri.indexOf(".jsp") > -1 || uri.equals("/")) {
					url = "/index.html";
					chain.doFilter(req, res);
				} else {
					uri = uri.substring(1, uri.length());
					url = "/#" + uri;
				}
				logger.debug(url);
				//request.getRequestDispatcher("/#" + uri).forward(request, response);
				response.setStatus(HttpServletResponse.SC_OK);
				response.setContentType("text/html");

				PrintWriter out = response.getWriter();

				out.println("<html>");
				out.println("<head>");
				out.println("<script>");
				out.println("location.href='" + url + "';");
				out.println("</script>");
				out.println("</head>");
				out.println("<body bgcolor=\"white\">");
				out.println("</body>");
				out.println("</html>");
			} else {
				chain.doFilter(req, res);
			}
		} catch (Exception e) {
			chain.doFilter(req, res);
		}
	}

	// true: convert
	// false: not convert
	public boolean isConvert(String uri) {
		logger.debug("==== ==== ==== ==== uri: " + uri);
		if (uri == null) {
			return false;
		}

		String[] patterns = new String[] { "/signin/facebook", "/user/facebook",
				"/signin/google","/ref/","swagger-ui.html","/user/socialsignup"};
		for (String auri : patterns) {
			if (uri.indexOf(auri) > -1) {
				// logger.debug("==== ==== uri: " + uri + "| ==== ==== auri: " + auri);
				return false;
			}
		}
		if (uri.equals("/")) {
			logger.debug("==== ==== ==== ==== uri2: " + uri);
			return false;
		}
		return true;
	}

	@Override
	public void destroy() {
		// TODO Auto-generated method stub

	}
}

```
