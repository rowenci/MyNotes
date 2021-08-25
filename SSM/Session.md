#Session机制
##

session是存在服务器中的一种缓存机制，可以验证用户是否已经登录过了

在SSM的登录接口的参数当中，需要加入HttpServletRequest request参数，用来获取请求携带的session

	HttpSession session = request.getSession();
	
	session.setAttribute("username",username)
	//可以通过这种方法来设置用户名session，以证明用户已经登录

	
	session.getAttribute("username")
	//读取session中是否有username，如果有，说明已经登录