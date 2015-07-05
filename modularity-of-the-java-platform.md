#Index
1. [What does module means](#what-does-module-means)  
2. [Why modularity](#why-modularity)  
3. [Feature](#feature)  
4. [Modularity Patterns](#modularity-patterns)  
5. [Jigsaw](#jigsaw)  
6. [OSGi](#osgi)  

#What does module means  
>“A software module is a deployable, manageable, natively reusable, composable, stateless unit of software that provides a concise interface to consumers”  

- Deployable: modules are unit of deployment  
- Manageable: modules are unit of management  
- Natively Reusable: modules are a unit of intraprocess reuse  
- Composable: modules are a unit of composition  
- Stateless: modules are stateless (*running software from a module has a state)  
- Testability: modules are a unit of testability  

#Why modularity
>"to tame complexity"  

>“Modularity is the best hope to reduce the costs of changes due to architectural decisions”  

#Feature  
- An autonomous unit of deployment  
  - loose coupling  
- A consistent and unique identity  
  - module ID and version  
- Easily identified and discovered requirements and dependencies  
  - standard compile-time and deployment facilities and meta-information  
- An open and understandable interface  
  - communication contract  
- Hidden implementation details  
  - encapsulation  

#Modularity Patterns  
- Base Patterns  
  - Base patterns advise to design and manage relationships between modules, that you have to emphasize reusability at software module level, and that modules shall be cohesive and have a behavior that serves a singular purpose  
- Dependency Patterns  
  - Dependency patterns teach you that there must be no cycles between modules, that should be organized in levels and in physical layers. And that a module should be independent from the runtime container and deployable independently from other modules  
- Usability, Extensibility, Utility Patterns  
  - The modules shall have a public interface that is well known  
  - Modules are configured externally  
  - For improving usability, create a façade serving as a coarse-grained entry point to another fine-grained module  
  - Restricted dependency to the abstract elements of the module so to improve extensibility and reduce the number of dependencies  
  - Rely heavily on the use of implementation factories and dependencies injection framework  
  - Separate abstractions from the classes that implement them in separate modules, so to help eliminate module relationships  
  - Perform a levelized build, repeatable build of the modules. This has positive effect on the lifecycle of software development as a levelized build integrate testing that require to integrate early and often, which guarantees a system health always in a good status  
  - Modules should have a test module  

#Jigsaw  
####Module  
- Module declared in module-info.java (module-info.class)  
- All new keywords are “scoped”  
<pre><code>
module B @ 1.0 {
       ...
}
</code></pre>

####Requires  
- Modules require other modules by name (and optionally, version)  
<pre><code>
module B @ 1.0 {
       require A @ [2.0,3.0);
}
</code></pre>

####Local  
- Requirements can be marked “local”  
- Target module is loaded in same classloader  
<pre><code>
module B @ 1.0 {
       require local A @ [2.0,3.0);
}
</code></pre>

####Exports  
- Modules list their exports, at package and type level  
- May include re-exported contents of required modules  
<pre><code>
module B @ 1.0 {
       require A @ [2.0,3.0);
       export org.foo.ClassFoo;
       export org.bar.*;
}
</pre></code>

####Friends  
- Modules can control which other modules require them  
- Compare with “friend” classes in C++  
- N.B. permit clause is not versioned  
<pre><code>
module A @ 2.0 {
       permit B;
}
</pre></code>

####Provides  
- Modules can logically “provide” other modules names  
- Compare with “vir tual packages” in Debian  
- Supports substitution, but not refactoring (splits or joins)  
<pre><code>
module com.ibm.stax @ 1.0 {
       provide jdk.stax @ 2.0;
}
</pre></code>

####Entry Point  
- Modules can have a single entry-point class  
- Compare with Main-Class header in Jar manifests  
<pre><code>
module A @ 2.0 {
       permit B;
       class org.foo.Main;
}
</pre></code>

####Versions  
- Modules will be versioned  
- Requirements use exact version or a range  
- No version semantics beyond ordering  

#OSGi  
####Areas  
- Core: the OSGi Framework    
  - Modularity   
  - Services  
  - Lifecycle and Dynamicity  
  - Security  

- Enterprise: services & component on top of Core Framework  
  - Addressing Enterprise use-cases, such as:  
    - Service Distribution  
    - Component models and IoC  
    - Configuration and Management  
    - JavaEE integration  
    - Cloud Computing  

- Residential and embedded services/components  

####Modularity
- In OSGi Modules are called Bundles  
- With OSGi Modularity you must declare what a module provides and what it needs  
  - Typically Java packages  
  - Can be other capabilities  
- Everything not explicitly declared as provided is internal -> not accessible to other modules  
- Improves maintainability  
  - as module boundaries and function must be made clear  
  - ￼tends to improve cohesion  
    - and reduce coupling  
- Non-monolithic  
  - allow for fine-grained updates of the system  
- Concurrent versions  
  - multiple versions of the same module can co-exist  
  - allows for gradual upgrades  

OSGi Modularity in Practice
- From a Java point of view. Just use classes as normal:  
<pre><code>
package org.acme.package1;
import org.acme.package2.MyClass;
...
MyClass ax = new MyClass();
ax.foo()
</pre></code>
- OSGi Module Metadata in MANIFEST.MF  
<pre><code>
Export-Package: org.acme.package1; version=1.2
Import-Package: org.acme.package2; version=[1.1, 2)
</pre></code>
- Compiler support by some, e.g Eclipse javac  
- Runtime enforcement by OSGi Framework  

####Services
- Services are Java Objects (POJOs)  
  - registered by Bundles  
  - consumed by Bundles  
- “SOA inside the JVM”  
- Services looked up by type and/or custom filter  
  - “I want a service that implements org.acme.Payment where location=US”  
  - One or many  
- Dynamic! Services can be updated without taking down the consumers  
  - OSGi Service Consumers react to dynamism  

How can we increase software reuse?  
- With OSGi Services there is no direct link between the service consumer and provider  
  - not even a text or XML file somewhere  
  - they communicate through a predefined API  
  - improves re-usability  
- In OSGi software reuse is visibly much higher than elsewhere  
- Given clear APIs implementations can be swapped  
  - even at runtime  
- OSGi standardizes some Service APIs  
  - organizations sometimes do the same  

How can I tailor my services more effectively?  
- Customizations are needed for  
  - Premium customers vs community users  
  - Customers who bought specific functionality  
  - Government vs commercial customers  
  - Services can be used to swap in/out customizatios  
- To tailor a service, just provide an alternative for a given API  
- Services can be selected based on API and Properties  
- Properties can be used to find the right set of services for a given customer  

I need to replace a Service with an alternative technology  
- If the API of the new technology can be mapped to the existing service  
  - Create an OSGi Service that wraps the alternative  
  - Replace the bundle providing the existing service with another bundle providing the wrapped technology  
- If no mapping can take place  
  - OSGi can still help through multiple concurrent versions  
  - Gradual migration  

OSGi Services and Bugfixes  
- OSGi Service-based development can help a lot here  
- Let's say Service A1 contains a bug  
- Service A1 has many active clients  
- We don't want to kill the client  
- But new clients should use the fixed service  
- Service A2 is a fixed version of the service  
  - Implementation has changed, API hasn't  
  - Install bundle with Service A2  
  - Service A1 and A2 exist concurrently  
  - Uninstall bundle with Service A1  
    - Old clients can still finish using the service  
    - Will not be handed out to new clients  

OSGi Services Summary  
- SOA within the Java VM  
- Multiple services can provide the same API  
- Dynamic (can come and go at runtime)  
  - and OSGi Service Consumers know how to deal with this  
- Lookup based on LDAP-style filters  
  - look up one or many  
- OSGi Services are normally within the Java VM  
- also: Distributed OSGi Services  
  - OSGi Remote Services specifications  

####OSGi Core Specification (R5)  
- Modularity layer and Bundle specification  
- Services layer  
  - Service Registry  
- Life Cycle layer  
  - Everything dynamic! add, remove, start, stop, update bundles  
- Security layer  
- Generic capabilities and requirements model  
- ... and some low-level building blocks ...  
- Addresses Enterprise use-cases  
- Standard Service APIs  
  - Configuration  
    - Config Admin / MetaType specs  
  - Component models (IoC)  
    - Blueprint and Declarative Services  
  - Eventing  
    - Event Admin spec  
  - ￼Service Distribution  
    - Remote Services and Remote Service Admin  
  - Repository specification  
  - Subsystems (multi-bundle deployment entities)  
  - JavaEE integration (JTA, JNDI, JPA, JDBC, ...)  
  ... and more ...  

####OSGi Resource
[http://karaf.apache.org/](#http://karaf.apache.org/)  
Apache Karaf is a small OSGi based runtime which provides a lightweight container onto which various components and applications can be deployed.  
