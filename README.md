# Sistema de Orçamento

**Descrição**: Neste Labs vamos implantar uma aplicação escrita em Java/Kotlin no  serviço Elastic Kubernetes Service da Amazon. A aplicação é um exemplo  do padrão CQRS que contempla dois serviços Quarkus que se comunicam  através de um barramento assíncrono usando o Kafka. Você vai aprender a  criar os manifestos do Kubernetes para implantação no EKS e quais  configurações são necessárias para ter o ambiente rodando em produção.

## Anotações

> Quarkus is a full-stack, Kubernetes-native [Java framework](https://www.redhat.com/en/topics/cloud-native-apps/what-is-a-Java-framework) made for Java virtual machines (JVMs) and native compilation,  optimizing Java specifically for containers and enabling it to become an effective platform for [serverless](https://www.redhat.com/en/topics/cloud-native-apps/what-is-serverless), [cloud](https://www.redhat.com/en/topics/cloud), and [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes) environments.
>
> Quarkus is designed to work with popular Java standards, frameworks, and  libraries like Eclipse MicroProfile and Spring (demonstrated together as part of a session in [this Red Hat Summit 2020 track](https://redhat.lookbookhq.com/c/red-hat-summit-virtu-11?x=QBJ6Wl&sc_cid=7013a000002DgCjAAK)), as well as Apache Kafka, RESTEasy (JAX-RS), Hibernate ORM (JPA), Spring, Infinispan, Camel, and many more.
>
> Quarkus’ dependency injection solution is based on CDI (contexts and dependency  injection) and includes an extension framework to expand functionality  and to configure, boot, and integrate a framework into your application. Adding an [extension](https://quarkus.io/extensions/) is as easy as adding a dependency, or you can use Quarkus tooling.
>
> It also provides the correct information to GraalVM (a universal virtual  machine for running apps written in a number of languages, including  Java and JavaScript) for native compilation of your application.																				[**What is Quarkus**](https://www.redhat.com/en/topics/cloud-native-apps/what-is-quarkus)

- [**Kubernetes**](https://azure.microsoft.com/en-in/topic/kubernetes-vs-docker/) é um orquestrador *open-source* que provê uma **API** para controlar como e onde os containers irão rodar. **Kubernete** permite orquestrar um *cluster* de maquinas virtuais e programar containers para rodar nestas maquinas virtuais baseado nos recursos computacionais disponiveis e nos recursos requeridos para cada container. Containers são agrupados dentro de *pods*, do qual é a unidade basica operacional para os **Kubernetes**. Estes containers e *pods* podem ser escalados para um estado desajado e é possivel administrar o seu ciclo de vida para manter a aplicação *up* e rodando. 

> GraalVM is a high-performance runtime that provides significant improvements in application performance and efficiency which is ideal for microservices. It is designed for applications written in Java, JavaScript, LLVM-based languages such as C and C++, and other dynamic languages. It removes the isolation between programming languages and enables interoperability in a shared runtime. It can run either standalone or in the context of OpenJDK, Node.js or Oracle Database. 													[**Why GraalVM?**](https://www.graalvm.org/why-graalvm/)

- Assim como o **Spring Boot** o **quarkus** é possivel gerar o projeto no [*site*](https://code.quarkus.io/) deles.  Ou apartir do terminal digitar o seguinte comando: 

  ```bash
  mvn io.quarkus:quarkus-maven-plugin:1.13.0.Final:create \
      -DprojectGroupId=org.acme \
      -DprojectArtifactId=getting-started \
      -DclassName="org.acme.getting.started.GreetingResource" \
      -Dpath="/hello"
  cd getting-started
  ```

- Para adicionar outras dependência deve usar o comando :

  ```bash
  ./mvnw quarkus:add-extension -Dextensions="hibernate-validator"
  ```

- Para listar as dependências que o **quarkus** possui deve usar o comando:

  ```bash
  ./mvnw quarkus:list-extensions
  ```

  Para compilar o projeto no modo de desenvolvimento deve usar o comando abaixo:

  ```
  ./mvnw compile quarkus:dev
  ```

  

## Erros

- Eu tive apenas um erro, relacionado a memoria disponivel ao **docker**. Na minha máquina o **docker** está configurado para usar 8 GB de memoria-ram, porém ao gerar o pacote dava o erro ao gerar a imagem do pacote (codigo 137, do qual está relacionado com a memoria). 

  ```bash
  Error: Image build request failed with exit status 137
  [INFO] ------------------------------------------------------------------------
  [INFO] BUILD FAILURE
  [INFO] ------------------------------------------------------------------------
  [INFO] Total time:  04:23 min
  [INFO] Finished at: 2021-04-05T16:49:55-03:00
  [INFO] ------------------------------------------------------------------------
  [ERROR] Failed to execute goal io.quarkus:quarkus-maven-plugin:1.13.0.Final:build (default) on project balance-service: Failed to build quarkus application: io.quarkus.builder.BuildException: Build failure: Build failed due to errors
  [ERROR] 	[error]: Build step io.quarkus.deployment.pkg.steps.NativeImageBuildStep#build threw an exception: java.lang.RuntimeException: Failed to build native image
  [ERROR] 	at io.quarkus.deployment.pkg.steps.NativeImageBuildStep.build(NativeImageBuildStep.java:209)
  [ERROR] 	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
  [ERROR] 	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
  [ERROR] 	at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
  [ERROR] 	at java.base/java.lang.reflect.Method.invoke(Method.java:566)
  [ERROR] 	at io.quarkus.deployment.ExtensionLoader$2.execute(ExtensionLoader.java:920)
  [ERROR] 	at io.quarkus.builder.BuildContext.run(BuildContext.java:277)
  [ERROR] 	at org.jboss.threads.EnhancedQueueExecutor$Task.run(EnhancedQueueExecutor.java:2415)
  [ERROR] 	at org.jboss.threads.EnhancedQueueExecutor$ThreadBody.run(EnhancedQueueExecutor.java:1452)
  [ERROR] 	at java.base/java.lang.Thread.run(Thread.java:834)
  [ERROR] 	at org.jboss.threads.JBossThread.run(JBossThread.java:501)
  [ERROR] Caused by: java.lang.RuntimeException: Image generation failed. Exit code was 137 which indicates an out of memory error. Consider increasing the Xmx value for native image generation by setting the "quarkus.native.native-image-xmx" property
  [ERROR] 	at io.quarkus.deployment.pkg.steps.NativeImageBuildStep.imageGenerationFailed(NativeImageBuildStep.java:345)
  [ERROR] 	at io.quarkus.deployment.pkg.steps.NativeImageBuildStep.build(NativeImageBuildStep.java:181)
  [ERROR] 	... 10 more
  [ERROR] -> [Help 1]
  [ERROR] 
  [ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
  [ERROR] Re-run Maven using the -X switch to enable full debug logging.
  [ERROR] 
  [ERROR] For more information about the errors and possible solutions, please read the following articles:
  [ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/MojoExecutionException
  ```

- Mais tarde, descobri que ao rodar o `sudo mvn clean package -Dnative -Dnative-image.docker-build=true`  a *build* ia com sucesso, pois a permissão `sudo` dava todos 8 GB para gerar a imagem ao **docker**. Porém, além da maquina travar, o pacote `target` onde fica a *build* vinha com restrições para o usuario ou usuarios do sistema operacional.

- Pois bem, se o super o usuario concede ao **docker** 8 GB, então faltava encontrar uma *flag* que fazia o mesmo para os usuarios comuns do sistema operacional. E a dica para isso está no *output* acima: `Consider increasing the Xmx value for native image generation by setting the "quarkus.native.native-image-xmx" property`. É possivel colocar o valor `Xmx` no **pom.xml** ou no comando que gera o pacote nativo: ` mvn clean package -Dnative -Dnative-image.docker-build=true -Dnative-image.xmx=6g`. 

- Neste comando, foi consedido ao **docker** usar 6 GB de memoria-ram a partir de um usuario comum do sistema. Dando uma folga, pois ainda restou 2 GB e a minha maquina não travou :smile:.

  ```bash
  [INFO] Scanning for projects...
  [INFO] 
  [INFO] ------------------< lab.aulaDIO:transaction-service >-------------------
  [INFO] Building transaction-service 1.0.0-SNAPSHOT
  [INFO] --------------------------------[ jar ]---------------------------------
  [INFO] 
  [INFO] --- maven-clean-plugin:2.5:clean (default-clean) @ transaction-service ---
  [INFO] 
  [INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ transaction-service ---
  [INFO] Using 'UTF-8' encoding to copy filtered resources.
  [INFO] Copying 3 resources
  [INFO] 
  [INFO] --- maven-compiler-plugin:3.8.1:compile (default-compile) @ transaction-service ---
  [INFO] Changes detected - recompiling the module!
  [INFO] Compiling 6 source files to /home/ssl/Documents/workspace/bank-account/transaction-service/target/classes
  [INFO] 
  [INFO] --- maven-resources-plugin:2.6:testResources (default-testResources) @ transaction-service ---
  [INFO] Using 'UTF-8' encoding to copy filtered resources.
  [INFO] skip non existing resourceDirectory /home/ssl/Documents/workspace/bank-account/transaction-service/src/test/resources
  [INFO] 
  [INFO] --- maven-compiler-plugin:3.8.1:testCompile (default-testCompile) @ transaction-service ---
  [INFO] Changes detected - recompiling the module!
  [INFO] Compiling 2 source files to /home/ssl/Documents/workspace/bank-account/transaction-service/target/test-classes
  [INFO] 
  [INFO] --- maven-surefire-plugin:3.0.0-M5:test (default-test) @ transaction-service ---
  [INFO] Tests are skipped.
  [INFO] 
  [INFO] --- maven-jar-plugin:2.4:jar (default-jar) @ transaction-service ---
  [INFO] Building jar: /home/ssl/Documents/workspace/bank-account/transaction-service/target/transaction-service-1.0.0-SNAPSHOT.jar
  [INFO] 
  [INFO] --- quarkus-maven-plugin:1.13.0.Final:build (default) @ transaction-service ---
  [INFO] [org.jboss.threads] JBoss Threads version 3.2.0.Final
  [INFO] [io.quarkus.flyway.FlywayProcessor] Adding application migrations in path 'file:/home/ssl/Documents/workspace/bank-account/transaction-service/target/transaction-service-1.0.0-SNAPSHOT.jar!/db/migration/' using protocol 'jar'
  [INFO] [org.hibernate.Version] HHH000412: Hibernate ORM core version 5.4.29.Final
  [INFO] [io.quarkus.deployment.pkg.steps.JarResultBuildStep] Building native image source jar: /home/ssl/Documents/workspace/bank-account/transaction-service/target/transaction-service-1.0.0-SNAPSHOT-native-image-source-jar/transaction-service-1.0.0-SNAPSHOT-runner.jar
  [INFO] [io.quarkus.deployment.pkg.steps.NativeImageBuildStep] Building native image from /home/ssl/Documents/workspace/bank-account/transaction-service/target/transaction-service-1.0.0-SNAPSHOT-native-image-source-jar/transaction-service-1.0.0-SNAPSHOT-runner.jar
  [WARNING] [io.quarkus.deployment.pkg.steps.NativeImageBuildStep] Cannot find the `native-image` in the GRAALVM_HOME, JAVA_HOME and System PATH. Install it using `gu install native-image` Attempting to fall back to container build.
  [INFO] [io.quarkus.deployment.pkg.steps.NativeImageBuildContainerRunner] Using docker to run the native image builder
  [INFO] [io.quarkus.deployment.pkg.steps.NativeImageBuildContainerRunner] Checking image status quay.io/quarkus/ubi-quarkus-native-image:21.0.0-java11
  21.0.0-java11: Pulling from quarkus/ubi-quarkus-native-image
  Digest: sha256:becf08de869e707beaa5e57444b533ef93ebef15aad90c92ac660ddf7cea2b11
  Status: Image is up to date for quay.io/quarkus/ubi-quarkus-native-image:21.0.0-java11
  quay.io/quarkus/ubi-quarkus-native-image:21.0.0-java11
  [INFO] [io.quarkus.deployment.pkg.steps.NativeImageBuildStep] Running Quarkus native-image plugin on GraalVM Version 21.0.0 (Java Version 11.0.10+8-jvmci-21.0-b06)
  [INFO] [io.quarkus.deployment.pkg.steps.NativeImageBuildRunner] docker run --env LANG=C --user 1000:1000 --rm -v /home/ssl/Documents/workspace/bank-account/transaction-service/target/transaction-service-1.0.0-SNAPSHOT-native-image-source-jar:/project:z quay.io/quarkus/ubi-quarkus-native-image:21.0.0-java11 -J-Djava.util.logging.manager=org.jboss.logmanager.LogManager -J-DCoordinatorEnvironmentBean.transactionStatusManagerEnable=false -J-Dsun.nio.ch.maxUpdateArraySize=100 -J-Dvertx.logger-delegate-factory-class-name=io.quarkus.vertx.core.runtime.VertxLogDelegateFactory -J-Dvertx.disableDnsResolver=true -J-Dio.netty.leakDetection.level=DISABLED -J-Dio.netty.allocator.maxOrder=1 -J-Duser.language=en -J-Duser.country=US -J-Dfile.encoding=UTF-8 --initialize-at-build-time= -H:InitialCollectionPolicy=com.oracle.svm.core.genscavenge.CollectionPolicy\$BySpaceAndTime -H:+JNI -H:+AllowFoldMethods -jar transaction-service-1.0.0-SNAPSHOT-runner.jar -H:FallbackThreshold=0 -H:+ReportExceptionStackTraces -H:-AddAllCharsets -H:EnableURLProtocols=http,https --enable-all-security-services -H:NativeLinkerOption=-no-pie --no-server -H:-UseServiceLoaderFeature -H:+StackTrace transaction-service-1.0.0-SNAPSHOT-runner
  [transaction-service-1.0.0-SNAPSHOT-runner:25]    classlist:  12,833.96 ms,  1.18 GB
  [transaction-service-1.0.0-SNAPSHOT-runner:25]        (cap):   2,758.02 ms,  1.18 GB
  [transaction-service-1.0.0-SNAPSHOT-runner:25]        setup:   7,410.42 ms,  1.19 GB
  11:59:57,163 INFO  [org.hib.val.int.uti.Version] HV000001: Hibernate Validator 6.2.0.Final
  11:59:57,418 INFO  [org.hib.Version] HHH000412: Hibernate ORM core version 5.4.29.Final
  11:59:57,469 INFO  [org.hib.ann.com.Version] HCANN000001: Hibernate Commons Annotations {5.1.2.Final}
  11:59:57,586 INFO  [org.hib.dia.Dialect] HHH000400: Using dialect: io.quarkus.hibernate.orm.runtime.dialect.QuarkusPostgreSQL10Dialect
  12:00:57,104 INFO  [org.jbo.threads] JBoss Threads version 3.2.0.Final
  [transaction-service-1.0.0-SNAPSHOT-runner:25]     (clinit):   1,835.41 ms,  2.94 GB
  [transaction-service-1.0.0-SNAPSHOT-runner:25]   (typeflow):  76,346.36 ms,  2.94 GB
  [transaction-service-1.0.0-SNAPSHOT-runner:25]    (objects):  65,244.21 ms,  2.94 GB
  [transaction-service-1.0.0-SNAPSHOT-runner:25]   (features):   2,723.22 ms,  2.94 GB
  [transaction-service-1.0.0-SNAPSHOT-runner:25]     analysis: 150,669.36 ms,  2.94 GB
  [transaction-service-1.0.0-SNAPSHOT-runner:25]     universe:   6,323.55 ms,  2.94 GB
  [transaction-service-1.0.0-SNAPSHOT-runner:25]      (parse):  31,379.09 ms,  3.77 GB
  [transaction-service-1.0.0-SNAPSHOT-runner:25]     (inline):  30,911.82 ms,  4.91 GB
  [transaction-service-1.0.0-SNAPSHOT-runner:25]    (compile): 120,930.13 ms,  5.19 GB
  [transaction-service-1.0.0-SNAPSHOT-runner:25]      compile: 189,804.90 ms,  5.19 GB
  [transaction-service-1.0.0-SNAPSHOT-runner:25]        image:  16,301.68 ms,  5.27 GB
  [transaction-service-1.0.0-SNAPSHOT-runner:25]        write:  55,323.21 ms,  5.27 GB
  [transaction-service-1.0.0-SNAPSHOT-runner:25]      [total]: 441,532.49 ms,  5.27 GB
  [INFO] [io.quarkus.deployment.QuarkusAugmentor] Quarkus augmentation completed in 577763ms
  [INFO] ------------------------------------------------------------------------
  [INFO] BUILD SUCCESS
  [INFO] ------------------------------------------------------------------------
  [INFO] Total time:  10:40 min
  [INFO] Finished at: 2021-04-06T09:08:56-03:00
  [INFO] -----------------------------------------------------------------------
  ```

- No caso de quem estiver usando o **Windows** ou **Mac OS** é possivel aumentar a memoria concedida ao **docker** a partir do **GUI**  que o **docker** possui para estes sistemas operacionais.

  >  GraalVM native-image tool needs a **lot of memory**! It needs at least 6GB but if you can give him 8GB it's better.
  >
  > To give you an idea, when I build a native image I almost close everything that run on my 16Gb laptop.
  >
  > Can you try again to build the native image with more memory  available? If you are using Docker for Windows take care to have  sufficient memory to the VM it uses (8Gb at least).
  >
  > If you run via docker you can set `Xmx` on the command line:
  >
  > ```bash
  > mvn clean package -Dnative -Dnative-image.docker-build=true -Dnative-image.xmx=6g
  > ```
  >
  > "[Out of memory when building a native executable in a container with a multi-stage Docker build](https://stackoverflow.com/questions/57911020/out-of-memory-when-building-a-native-executable-in-a-container-with-a-multi-stag)" - 

