# 第四节：从 Retrofit 的原理来看 HTTP

## 本质
* retrofit的本质是动态代理模式,主要代码如下：

        Retrofit.java

        public <T> T create(final Class<T> service) {
            return (T) Proxy.newProxyInstance(service.getClassLoader(), new Class<?>[] { service },
                new InvocationHandler() {
                  @Override public Object invoke(Object proxy, Method method, Object... args)
                      throws Throwable {
                        ......其他代码......

                        // 1. 将每一个接口里的方法封装为ServiceMethod对象
                        ServiceMethod serviceMethod = loadServiceMethod(method);
                        // 2. 将retrofit的call和okhttp的call连接起来
                        OkHttpCall okHttpCall = new OkHttpCall<>(serviceMethod, args);
                        // 3. 通过serviceMethod里的callAdapter，包装并调用okhttpCall，比如线程切换、转入rxjava里的其他线程等
                        return serviceMethod.callAdapter.adapt(okHttpCall);
                  }
                });
          }

* loadServiceMethod(method)
	* 通过反射的method对象获取一个ServiceMethod对象
	* 创建ServiceMethod对象的主要方法

         	public ServiceMethod build() {
            		// “创建”callAdapter，其实是从retrofit对象的adapterFactories中获取
                  callAdapter = createCallAdapter();
                  responseType = callAdapter.responseType();

                  // “创建”convert，其实是从retrofit对象的converterFactories中获取
                  responseConverter = createResponseConverter();
                  for (Annotation annotation : methodAnnotations) {
                    parseMethodAnnotation(annotation);
                  }
                  int parameterCount = parameterAnnotationsArray.length;

                  // 根据接口方法里的参数创建ParameterHandler，并将各个参数放到里面
                  parameterHandlers = new ParameterHandler<?>[parameterCount];
                  for (int p = 0; p < parameterCount; p++) {
                    Type parameterType = parameterTypes[p];
                    Annotation[] parameterAnnotations = parameterAnnotationsArray[p];
                    parameterHandlers[p] = parseParameter(p, parameterType, parameterAnnotations);
                  }
                  return new ServiceMethod<>(this);
            }
	* 作用：
		* 获取接口里方法的各种属性，如annotation、返回值等，并根据相关属性从retrofit对象中获取对应的callAdapter和converter
		* 根据参数及其annotation封装在parameterHandlers中、为后面的网络请求做准备

## 代理模式
通过一个接口，来动态生成这个接口的实体类，并在这个实体类的所有方法的前后进行一些操作
* Proxy
    * 作用是生成接口实体类
    * 本质：
    	* 通过类似字符串拼接的方法，生成一个本地的类，并且使这个类继承传进去的一个或多个interface
    	* 将这个类进行动态实例化（如cglib、java自带的complier api等）
* InvocationHandler
	* 类里面的invoke方法负责调用生成的接口实体类对象里的所有方法，并且可以在调用前后做一些自定义的操作
	* 如何调用：method.invoke（反射）