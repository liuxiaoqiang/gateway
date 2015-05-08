-   [Home](../../index.md)
-   [Documentation](../index.md)
-   Inject Bytes into a Custom Protocol

Inject Bytes into a Custom Protocol  ![This feature is available in KAAZING Gateway - Enterprise Edition](images/enterprise-feature.png)
===========================================================================

In this procedure, you will learn how to inject bytes into a custom protocol.

Before You Begin
----------------

Learn about protocol injection in [About Protocol Injection](c_aaa_inject.md). To complete the steps in this topic, you must be familiar with the bytes (and the necessary encoding) that your back-end server requires.

To Inject Bytes into a Custom Protocol
--------------------------------------

1.  Create a new login module.

    **Note:** We recommend that you create a separate LoginModule specifically for the purpose of protocol injection. The steps in this topic assume that you already have an existing login module (either one supplied from the Gateway or one that you've created) that establishes the identity associated with this connection and authenticates it.

    In the login module, determine the identity or any other information you want to send to the back-end server. For example, you may want to inspect the Principals from other login modules in the chain to discover the identity associated with the client connection.

2.  Instantiate a new object of type `org.kaazing.gateway.server.spi.ProtocolInjection` and override the `getName()` and `getInjectableBytes()` methods.

    -   The `getName()` method should specify the name of this Principal to avoid conflicting with other Principals of the same type.
    -   The `getInjectableBytes()` returns the bytes which will be injected into the connection to the back-end server. The bytes must conform to the protocol that the server is expecting. Using this method, incorporate the identity or other information determined in step 1.
   
    See the [Protocol Injection](../apidoc/server/gateway/server/spi/index.html) documentation for more information.

3.  In your login module's `login()` method, add the newly created `ProtocolInjection` object (which is of type Principal) into the Subject. This causes the Gateway to call its `getInjectableBytes()` method and inject the bytes into the custom protocol.
4.  Compile your LoginModule class into a JAR file and put the JAR file in `GATEWAY_HOME/lib`.
5.  Update the Gateway configuration located in `GATEWAY_HOME/conf/` to add your new login module to the chain. This login module must be located after any other login modules on which it depends. The following configuration example shows the sample login module:

    ``` xml
    <security>
        ...
      <login-modules>
        <login-module>
        <type>file</type>
          <success>required</directory>
          <options>
            <file>jaas-config.xml</file>
          </options>
        </login-module>
        <login-module>
            <type>class:com.example.MyInjectionLoginModule</type>
        </login-module>
      </login-modules>
    </security>
    ```

    Note: For information about login modules, see [Configure a Chain of Login Modules](p_aaa_config_lm.md). the Gateway includes an [SPI (Service Provider Interface)](../apidoc/server/gateway/server/spi/index.md) called `ProtocolInjection`.

6.  Start (or restart) the Gateway, then connect a new client.

    After authentication succeeds, the Gateway establishes a connection to the back-end server. At this point, if a Principal of type `ProtocolInjection` is present, the Gateway sends the specified bytes to the back-end server before any data from the client are sent.


Notes
-----

-   You can see a complete example of a sample login module that uses protocol injection by opening: `GATEWAY_HOME/web/extras/samples/security/IdentityInjectionLoginModule.java`.
-   For more information, see the [SPI (Service Provider Interface)](../apidoc/server/gateway/server/spi/index.md).

Next Step
---------

You have completed implementing protocol injection with the Gateway.

See Also
------------------------------

-   [About Protocol Injection](c_aaa_inject.md)
-   [Configure the Gateway](../admin-reference/o_conf_checklist.md)
-   [About Authentication and Authorization](c_aaa_aaa.md)