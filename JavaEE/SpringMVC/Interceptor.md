#拦截器(Interceptor)
##

拦截器是基于AOP，用于对处理器进行预处理和后处理

首先，拦截器类Interceptor需要实现(implements)HandlerInterceptor接口

接口中提供3个方法

	1.preHandle
	进入Handler方法之前执行
	可用于身份认证、身份授权(登录认证、权限校验)
	比如身份认证，如果认证不通过，表明用户没有登录，需要此方法进行拦截，不再向下执行
	该方法的返回值 false(表示拦截，不向下执行)
				  true(表示放行，继续向下执行)

	2.postHandle
	进入Handler方法之后，返回modelAndView之前执行
	应用场景从modelAndView出发：将公用的模型数据(如菜单导航)在这里传到视图，也可以在这里统一制定视图

	3.afterCompletion
	执行Handler方法之后执行
	可用于统一异常处理，统一日志处理

拦截器配置
	

当有两个拦截器且都放行的时候

	preHandle按顺序执行
	postHandle和afterCompletion按逆序执行

拦截器1放行，拦截器2不放行的时候
	
	拦截器1放行，拦截器2的preHandle方法才执行
	拦截器2的preHandle方法不放行，拦截器2的postHandle和afterCompletion不执行

只要有一个拦截器不放行，postHandle方法就不会执行

两个拦截器都不放行的时候

	拦截器1preHandle不放行，postHandle和afterHandle都不执行
	拦截器1preHandle不放行，拦截器2不执行

>比如：统一日志处理拦截器，需要该拦截器preHandle一定要放行，且将它放在拦截器链接中的第一个位置
>
>比如：登陆认证拦截器，放在拦截器链接中第一个位置；权限校验拦截器放在登陆认证拦截器之后(因为登陆通过后进行权限校验)