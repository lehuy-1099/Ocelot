Tracing
=======

This page details how to perform distributed tracing with Ocelot. 

OpenTracing
-----------

Ocelot providers tracing functionality from the excellent `OpenTracing C# <https://github.com/opentracing/opentracing-csharp>`_ project. 
The code for the Ocelot integration can be found `here <https://github.com/ThreeMammals/Ocelot.Tracing.OpenTracing>`_.

The example below uses `Jaeger C# <https://github.com/jaegertracing/jaeger-client-csharp>`_ client to provide the tracer used in Ocelot.
In order to add OpenTracing services we must call the ``AddOpenTracing()`` extension of the ``OcelotBuilder`` being returned by ``AddOcelot()`` [#f1]_ like below:

.. code-block:: csharp

    services.AddSingleton<ITracer>(sp =>
    {
        var loggerFactory = sp.GetService<ILoggerFactory>();
        Configuration config = new Configuration(context.HostingEnvironment.ApplicationName, loggerFactory);

        var tracer = config.GetTracer();
        GlobalTracer.Register(tracer);
        return tracer;
    });

    services
        .AddOcelot()
        .AddOpenTracing();

Then in your **ocelot.json** add the following to the Route you want to trace:

.. code-block:: json

      "HttpHandlerOptions": {
            "UseTracing": true
        },

Ocelot will now send tracing information to Jaeger when this Route is called.

Butterfly
---------

Ocelot providers tracing functionality from the excellent `Butterfly <https://github.com/liuhaoyang/butterfly>`_ project. The code for the Ocelot integration
can be found `here <https://github.com/ThreeMammals/Ocelot.Tracing.Butterfly>`_.

In order to use the tracing please read the Butterfly documentation.

In ocelot you need to do the following if you wish to trace a Route.

   ``Install-Package Ocelot.Tracing.Butterfly``

In your ``ConfigureServices`` method to add Butterfly services we must call the ``AddButterfly()`` extension of the ``OcelotBuilder`` being returned by ``AddOcelot()`` [#f1]_ like below:

.. code-block:: csharp

    services
        .AddOcelot()
        // this comes from Ocelot.Tracing.Butterfly package
        .AddButterfly(option =>
        {
            //this is the url that the butterfly collector server is running on...
            option.CollectorUrl = "http://localhost:9618";
            option.Service = "Ocelot";
        });

Then in your **ocelot.json** add the following to the Route you want to trace:

.. code-block:: json

      "HttpHandlerOptions": {
            "UseTracing": true
        },

Ocelot will now send tracing information to Butterfly when this Route is called.

""""

.. [#f1] The ``AddOcelot`` method adds default ASP.NET services to DI-container. You could call another more extended ``AddOcelotUsingBuilder`` method while configuring services to build and use custom builder via an ``IMvcCoreBuilder`` interface object. See more instructions in :doc:`../features/dependencyinjection`, "**The AddOcelotUsingBuilder method**" section.
