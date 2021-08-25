#SpringMVC拦截器配置
##

拦截器通常用来对用户是否登录进行判断，如果登录，那么就可以访问只能在登陆后才能访问的地址

首先是拦截器类的编写(拦截器类应当继承HandlerInterceptorAdapter)

	public class LoginInterceptor extends HandlerInterceptorAdapter {

		public boolean preHandle(HttpServletRequest request,
				HttpServletResponse response, Object handler) throws Exception {
	
			HttpSession session = request.getSession();
			User user = (User) session.getAttribute("usersession");
	
			if (user != null) {
				return true;
			} else {
				response.sendRedirect(request.getContextPath()
						+ "/welcomes/welcome.do");
				return false;
			}
		}
	
		/**
		 * 在业务处理器处理请求执行完成后,生成视图之前执行的动作 可在modelAndView中加入数据，比如当前时间
		 */
	
		public void postHandle(HttpServletRequest request,
				HttpServletResponse response, Object handler,
				ModelAndView modelAndView) throws Exception {
		}
	
		/**
		 * 在DispatcherServlet完全处理完请求后被调用,可用于清理资源等
		 * 
		 * 当有拦截器抛出异常时,会从当前拦截器往回执行所有的拦截器的afterCompletion()
		 */
	
		public void afterCompletion(HttpServletRequest request,
				HttpServletResponse response, Object handler, Exception ex)
				throws Exception {
		}
	
	}

在该类中的preHandle方法中可以向session中添加用户信息，在后面的操作中可以对用户身份进行判断

##

	<mvc:interceptors>
		<!-- 登录权限拦截器 -->
		<mvc:interceptor>
			<mvc:mapping path="/**" />
			<mvc:exclude-mapping path="/welcomeController/*"/>
			<mvc:exclude-mapping path="/loginController/login.do"/>
			<mvc:exclude-mapping path="/loginController/check.do"/>
			<mvc:exclude-mapping path="/loginController/exit.do"/>
			<mvc:exclude-mapping path="/registController/*"/>
			<bean class="com.interceptor.LoginInterceptor">
			</bean>
		</mvc:interceptor>
	</mvc:interceptors>

exclude-mapping当中的路径是不被拦截的路径

这是springmvc配置文件当中关于拦截器的配置
