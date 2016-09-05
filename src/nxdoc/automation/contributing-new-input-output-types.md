---
title: Contributing New Input-Output Types
labels:
    - marshalling
    - codec
confluence:
    ajs-parent-page-id: '22380844'
    ajs-parent-page-title: Automation
    ajs-space-key: NXDOC60
    ajs-space-name: Nuxeo Platform Developer Documentation — 6.0
    canonical: Contributing+New+Input-Output+Types
    canonical_source: >-
        https://doc.nuxeo.com/display/NXDOC60/Contributing+New+Input-Output+Types
    page_id: '22380843'
    shortlink: K4FVAQ
    shortlink_source: 'https://doc.nuxeo.com/x/K4FVAQ'
    source_link: /display/NXDOC60/Contributing+New+Input-Output+Types
history:
    - 
        author: Solen Guitter
        date: '2014-03-31 17:54'
        message: ''
        version: '2'
    - 
        author: Alain Escaffre
        date: '2014-03-31 09:34'
        message: ''
        version: '1'

---
{{! excerpt}}

This pages explains how to add new input/output types to the Automation Service.

{{! /excerpt}}

<span style="color: rgb(51,51,51);">Default input/output types are blob, document or void. You can extend the input/output types by contributing the new marshalling logic to automation.</span>

Marshalling and operation binding logic is done by selecting client and server side using the JSON type name. At this stage,&nbsp;we're always using the Java type simple name in lowercase. This makes the operation binding logic happy.

The logic you need to provide is as follow:

*   The JSON type name,
*   The POJO class,
*   A writing method that puts data extracted from the POJO object into the JSON object,
*   A reading method that gets data from the JSON object and builds a POJO object from it,
*   A reference builder that extracts the server reference from a POJO object,
*   A reference resolver that provides access to a POJO object giving a server reference.

Server and client do not share classes, so you need to provide two marshalling implementation classes.

Server side, you should provide a&nbsp;[codec](http://explorer.nuxeo.org/nuxeo/site/distribution/Nuxeo%20Platform-6.0/viewExtensionPoint/org.nuxeo.ecm.automation.server.AutomationServer--codecs). The implementation class is to be contributed to the automation server component using the&nbsp;`codecs`&nbsp;extension point.

```html/xml
  <extension target="org.nuxeo.ecm.automation.server.AutomationServer"
        point="codecs">
        <codec class="org.nuxeo.ecm.automation.server.test.MyObjectCodec" />
  </extension>
```

[`MyObjectCodec`](https://github.com/nuxeo/nuxeo-features/blob/release-6.0/nuxeo-automation/nuxeo-automation-test/src/test/java/org/nuxeo/ecm/automation/server/test/MyObjectCodec.java)&nbsp;class should extend&nbsp;[`org.nuxeo.ecm.automation.server.jaxrs.io.ObjectCodec`](https://github.com/nuxeo/nuxeo-features/blob/release-6.0/nuxeo-automation/nuxeo-automation-io/src/main/java/org/nuxeo/ecm/automation/io/services/codec/ObjectCodec.java). The most common codecs provided by default into the Nuxeo server are implemented into [`org.nuxeo.ecm.automation.server.jaxrs.io.ObjectCodecService`](https://github.com/nuxeo/nuxeo-features/blob/release-6.0/nuxeo-automation/nuxeo-automation-io/src/main/java/org/nuxeo/ecm/automation/io/services/codec/ObjectCodecService.java).

Client side, you should implement the&nbsp;`org.nuxeo.ecm.automation.client.jaxrs.spi.JsonMarshaller<T>`&nbsp;interface.&nbsp;The implementation class is to be registered to the automation client marshalling framework by invoking the static method&nbsp;`org.nuxeo.ecm.automation.client.jaxrs.spi.AbstractAutomationClient#registerPojoMarshaller(Clazz marshaller)`.

Here is an example of the&nbsp;[org.nuxeo.ecm.automation.client.jaxrs.spi.marshallers.BooleanMarshaller](https://github.com/nuxeo/nuxeo-features/blob/release-6.0/nuxeo-automation/nuxeo-automation-client/src/main/java/org/nuxeo/ecm/automation/client/jaxrs/spi/marshallers/BooleanMarshaller.java).

&nbsp;

* * *