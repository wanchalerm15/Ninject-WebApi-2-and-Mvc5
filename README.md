# Ninject Integration in WebApi 2 And Mvc5

**How to integrate Ninject in a WebApi 2 And Mvc5 Project**
<br>Test Mvc in Index page
<br>Test Web Api 2 calling the Get Method "IsServiceUp"
<br>(Attribute Routing used, example url: http://www.foo.com/{eventual subfolder}/IsServiceUp)
<br><br>
1. Create an Empty Web Application and select the checkboxes for Mvc and Web Api
2. Install Nuget Package: **Ninject.Web.WebApi.WebHost**
3. Install Nuget Package: **WebActivatorEx**
4. Create a class in **App_Start** named **NinjectWebCommon.cs**

    [assembly: WebActivatorEx.PreApplicationStartMethod(typeof(NinjectWebCommon), "Start")]
    [assembly: WebActivatorEx.ApplicationShutdownMethodAttribute(typeof(NinjectWebCommon), "Stop")]

    namespace YOURNAMESPACE<br>
    {<br>
        public static class NinjectWebCommon
        {
            private static readonly Bootstrapper bootstrapper = new Bootstrapper();

            public static void Start()
            {
                DynamicModuleUtility.RegisterModule(typeof(OnePerRequestHttpModule));
                DynamicModuleUtility.RegisterModule(typeof(NinjectHttpModule));
                bootstrapper.Initialize(CreateKernel);
            }

            public static void Stop()
            {
                bootstrapper.ShutDown();
            }

            private static IKernel CreateKernel()
            {
                var kernel = new StandardKernel();
                kernel.Bind<Func<IKernel>>().ToMethod(ctx => () => new Bootstrapper().Kernel);
                kernel.Bind<IHttpModule>().To<HttpApplicationInitializationHttpModule>();

                RegisterServices(kernel);
                return kernel;
            }
            private static void RegisterServices(IKernel kernel)
            {
              //kernel.Bind<IRepo>().ToMethod(ctx => new Repo("Ninject Rocks!"));
            }
        }
    }<br>
5. Configure your DI in RegisterServices
6. Add dependency to Mvc or Web Api Controllers constructor parameters
