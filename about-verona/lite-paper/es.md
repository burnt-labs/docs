# Verona Litepaper

## Hacer que la IA sea inteligente

La capa de inteligencia para la IA. Hechos verificados sobre el mundo real, propiedad del usuario y reutilizables por cualquier agente.

Junio de 2026

---

## Resumen

Verona es la capa de inteligencia para la IA. Hace que los datos verificados por el usuario sean portables, privados y programables, de modo que cualquier agente pueda realizar transacciones sobre información que el usuario realmente posee.

Hoy un agente puede hablar, pero no puede actuar, porque lo único que necesita, un hecho verificado que esté autorizado a usar, no existe en una forma que pueda alcanzar. Verona hace que ese hecho exista. Un hecho se verifica una vez en su fuente, el usuario lo posee, y cualquier agente autorizado por el usuario lo reutiliza sin que los datos subyacentes queden expuestos. La red combina verificación en la fuente, ejecución confiable, liquidación privada, verificación formal y pagos programables en stablecoin, y ya genera ingresos a partir de aplicaciones reales. Verifica una vez, reutiliza en todas partes, no expongas nada.

## 1 Introducción

### 1.1 La brecha

La IA puede escribir, planificar y mantener una conversación. No puede actuar sobre lo que es verdadero acerca de ti. Pide a un agente que renueve una receta, solicite un apartamento o refinancie un préstamo, y se detendrá en el primer paso que requiera un hecho verificado sobre tus ingresos, tu identidad o tu elegibilidad. El modelo es fluido y la tarea muere, porque la fluidez no equivale a saber que algo es verdadero. La inteligencia es la capacidad de actuar sobre lo que es verdadero, y un agente que no puede hacerlo aún no es inteligente.

### 1.2 No hay datos verificados sobre los que actuar

La verificación ocurre constantemente: comprobaciones de identidad, consultas de crédito, evaluaciones de ingresos. Cada una de ellas se descarta en el momento en que se usa, y el siguiente servicio empieza de nuevo. La persona descrita por los datos no posee nada de ello. Un agente que busca un hecho que esté autorizado a usar no encuentra nada, porque toda la pila fue construida para un ser humano al que se le podía pedir que subiera un documento más. Los agentes no suben documentos. Esta es la brecha que Verona cierra.

### 1.3 El desbloqueo

Verona verifica un hecho una vez, entrega la propiedad al usuario y permite que cualquier agente autorizado por el usuario lo reutilice. Los datos subyacentes permanecen donde están y no se muestran a nadie. Un solo hecho verificado es útil; una red de ellos, propiedad de las personas a las que describen y accesible para todos los agentes, es una nueva capa de internet. Verona se construye sobre cuatro ideas:

- Hechos verificados como primitivo - Una verificación se convierte en un activo duradero que el usuario posee, no en una comprobación de un solo uso.

- Liquidación privada - Los hechos y las acciones realizadas sobre ellos se liquidan en una cadena con estado público y privado híbrido.

- Verificación formal - La cadena se comprueba contra especificaciones verificadas por máquina, de modo que un agente pueda confiar en ella.

- Pagos programables - Los agentes liquidan valor en stablecoin automáticamente como parte de una acción.

## 2 Arquitectura central

Verona es una sola red. Las fuentes del mundo real la alimentan, cinco capacidades la hacen funcionar, y usuarios, agentes, empresas y las aplicaciones construidas encima extraen valor del mismo conjunto de hechos verificados.

![](../../.gitbook/assets/litepaper-image3.png)

### 2.1 Verificación en la fuente

Verona nunca te pide que entregues tus datos. Produce una prueba desde el sistema que ya contiene el hecho, y los datos brutos nunca se mueven. Cada prueba se adapta al origen del hecho: zkTLS para una sesión web, zkEmail para correo electrónico, zkPassport para un pasaporte, una atestación de aplicación o enclave para una aplicación, una atestación fotográfica como prueba de una persona real y presente, y zkDCap, una prueba de conocimiento cero en cadena de una atestación de hardware. Quien consume la prueba la verifica de forma independiente. Las pruebas y atestaciones se remontan a su fuente, todo el código de contratos inteligentes y de Verona es abierto y verificable, y ninguna parte de la pila exige una confianza no verificable en Verona. Un destinatario puede auditar tanto la procedencia de los datos como la validez de cualquier divulgación selectiva. La verificación en la fuente está activa hoy frente a proveedores reales de nómina, y devuelve una prueba de ingresos y empleo en segundos. No se almacena ningún documento.

![](../../.gitbook/assets/litepaper-image5.png)

### 2.2 Hechos que posees y reutilizas

Un hecho verificado se cifra para el usuario y se mantiene en la red. Cuando el usuario autoriza a un destinatario (un agente, un servicio u otro enclave), la red vuelve a cifrar el hecho para ese destinatario y un entorno de ejecución confiable lo transforma exactamente en la respuesta que el destinatario necesita. El destinatario recibe una respuesta y una prueba, nunca los datos. Nada se expone en tránsito, el usuario nunca tiene que custodiar una clave, y una verificación funciona para todos los destinatarios que el usuario autoriza. El trabajo hecho una vez no se repite.

Los datos almacenados solo pueden transformarse en la respuesta que el destinatario necesita, lo que no deja espacio para una afirmación falsa o fabricada. La gestión de claves se ejecuta dentro de enclaves de hardware sobre compilaciones verificables y reproducibles. El manejo de claves es la parte de mayor fricción y mayor riesgo de cualquier herramienta blockchain, y aquí desaparece por completo de la vista del usuario.

![](../../.gitbook/assets/litepaper-image1.png)

### 2.3 Liquidación privada

Los hechos y las acciones realizadas sobre ellos se liquidan en una única cadena con estado público y privado híbrido.

![](../../.gitbook/assets/litepaper-image9.png)

Lo que debe ser público es público. Lo que debe permanecer confidencial (saldos, términos, el contenido de un hecho verificado) permanece confidencial en la misma cadena. Como las claves se mantienen en enclaves de hardware y la pila de procedencia es abierta, los datos privados pueden producir salidas públicas en las que otros pueden confiar y sobre las que pueden actuar. No hay un segundo sistema añadido para la privacidad. Es una sola cadena.

### 2.4 Verificación formal

Verona está construida para ser verificada, no solo auditada. Cada contrato se comprueba contra una especificación verificada por máquina, y las especificaciones se producen mediante un proceso adversarial.

![](../../.gitbook/assets/litepaper-image6.png)

Varios modelos de frontera las redactan mientras otros atacan los borradores, y una especificación se acepta cuando sobrevive a ese escrutinio. Los modelos que simplemente están de acuerdo pueden compartir los mismos errores, que es exactamente lo que el paso adversarial existe para detectar. Este es el modelo de confianza sobre el que se construye la cadena, y es lo que permite que un agente trate el comportamiento de la cadena como algo de lo que puede depender, no como algo que solo puede esperar que funcione.

### 2.5 Pagos programables

Los rieles de pago nativos permiten que un agente pague por lo que hace en el momento en que lo hace, denominado en stablecoin, sin que una persona intervenga para aprobar cada transferencia. Verificación, acción y liquidación ocurren en un solo flujo, no en tres sistemas desconectados.

## 3 La red

Los usuarios convierten hechos del mundo real y digital en pruebas reutilizables. Los builders construyen aplicaciones sobre esas pruebas, las empresas verifican sus propios usuarios y flujos de trabajo, y los agentes actúan sin tener que confiar unos en otros ni manejar datos sensibles en bruto. Todas las partes extraen valor del mismo conjunto de hechos verificados, y eso es lo que convierte un producto en una red.

Verona se compone porque cada participante añade valor al mismo conjunto de hechos verificados: los usuarios añaden oferta, las aplicaciones, empresas y agentes crean demanda, los builders amplían dónde pueden usarse los hechos, los sistemas fuente aumentan lo que puede probarse, y los pagos y la liquidación convierten esa actividad en una economía.

![](../../.gitbook/assets/litepaper-image2.png)

- Los usuarios suministran hechos verificados y autorizados sobre sí mismos. Verifican una vez, poseen el resultado y deciden qué aplicaciones, empresas, agentes o enclaves pueden usarlo. A cambio reciben portabilidad, privacidad y reutilización, y con el tiempo una voz en cómo se monetizan los hechos que crean.

- Las aplicaciones crean demanda para esos hechos. Usan Verona para incorporar usuarios, comprobar elegibilidad, reducir fraude y completar flujos de trabajo sin recopilar ni almacenar datos sensibles en bruto. Cada aplicación da a los usuarios una razón más para verificar una vez y reutilizar en otro lugar.

- Los builders amplían la red. Lanzan aplicaciones, runtimes de agentes, flujos de trabajo e integraciones sobre primitivos de verdad, y evitan reconstruir por su cuenta la infraestructura de verificación, consentimiento y pagos que de otro modo tendrían que crear.

- Las empresas participan de dos maneras. Primero llegan para verificar sus propios usuarios, clientes, trabajadores, transacciones, credenciales y elegibilidad. A medida que se acumulan hechos verificados, se quedan para consumir y computar sobre datos autorizados, porque la verificación es el primer caso de uso y la inteligencia verificada es el más amplio.

- Los agentes ponen los hechos verificados a trabajar, para una persona, para una empresa o entre sí.

- Los sistemas fuente y verificadores amplían lo que la red puede saber. Proveedores de nómina, sistemas de identidad, bancos, correo electrónico, pasaportes, aplicaciones, dispositivos, plataformas de comercio y sistemas de salud producen cada uno una prueba adaptada al hecho que se verifica, y cada nueva fuente hace que los hechos en la red sean más amplios y mejores.

- Los participantes del token y de la red operan la capa económica subyacente, proporcionando liquidación, seguridad y liquidez para todo lo que está encima.

## 4 Qué impulsa

Estas son superficies hacia una sola red, no productos separados.

![](../../.gitbook/assets/litepaper-image4.png)

### 4.1 Verificación de atributos

La primera superficie es la verificación de atributos. Verona puede verificar cualquier atributo que un usuario tenga en un sitio web detrás de un inicio de sesión: ingresos y empleo, gasto, actividad, nivel de fidelidad, antigüedad de la cuenta, el tipo de datos que normalmente queda aislado sin forma de compartirse. El usuario inicia sesión, y la red devuelve una prueba criptográfica de que el atributo cumple una condición declarada. No se sube ningún documento y no se almacena ningún dato bruto. Es más rápido, más barato y más fiable que el proceso de carga de documentos al que reemplaza, y hoy está activo con empresas que pagan. La verificación de ingresos y empleo para el sector inmobiliario es un ejemplo. El mismo enfoque se extiende a préstamos, seguros y cualquier negocio que necesite actuar sobre un atributo verificado en lugar de uno autodeclarado, con cada prueba reutilizada entre destinatarios.

### 4.2 Agentes que actúan sobre hechos verificados

La segunda superficie son los agentes, y participan de más de una manera.

- Los agentes de usuario actúan por una persona, llevando los hechos verificados de esa persona a una tarea sin que el usuario tenga que volver a probar nada.

- Los agentes empresariales actúan solo sobre entradas verificadas, por lo que una decisión automatizada descansa sobre una prueba en lugar de una afirmación.

- De agente a agente, los sistemas autónomos transaccionan confiando en las pruebas de los demás en lugar de confiar entre sí, sin un humano en medio que responda por ellos.

Cada caso necesita lo mismo. Un hecho verificado sobre el que el agente pueda actuar, y cada uno reutiliza verificaciones que ya están en la red en lugar de empezar desde cero.

### 4.3 La capa de contexto verificado para builders

![](../../.gitbook/assets/litepaper-image7.png)

La tercera superficie son los builders. Una sola línea de configuración da a cualquier runtime de agentes que hable el protocolo estándar acceso a hechos verificados y consentidos por el usuario. Los builders empiezan desde primitivos de verdad y evitan reconstruir desde cero verificación, cumplimiento, pagos e ingesta de datos. Verona se sitúa entre la identidad y las herramientas como la capa que suministra contexto verificado, y las pruebas que devuelve son reutilizables en todos los demás servicios de la red.

## 5 Por qué se compone

Un producto normal de verificación empieza de nuevo cada vez: un usuario prueba algo, una aplicación lo comprueba, el resultado muere dentro de ese flujo de trabajo, y nada se acumula.

Verona funciona de otra manera. Cada hecho verificado se convierte en un primitivo reutilizable controlado por el usuario, lo que da a aplicaciones y agentes más contexto confiable sobre el cual actuar. Nuevas aplicaciones dan a los usuarios más razones para verificarse. Nuevos flujos empresariales crean demanda de hechos que sean precisos, autorizados y respaldados por una fuente. Nuevas fuentes amplían lo que la red puede probar.

![](../../.gitbook/assets/litepaper-image8.png)

La oferta y la demanda se refuerzan entre sí, y la red se fortalece con el uso. Un modelo que verifica y descarta se reinicia con cada comprobación y no acumula nada, por lo que la ventaja se amplía con el tiempo.

El foso es la red creciente de hechos verificados reutilizables, propiedad de las personas a las que describen y accesible para cualquier aplicación, empresa o agente autorizado. Ninguna prueba, cliente o aplicación individual podría copiarse para replicarla.

## 6 Hacia dónde conduce

Verona empieza verificando hechos para aplicaciones. Esa es la primera cuña: comprobaciones de ingresos más rápidas, verificación de empleo, comprobaciones de elegibilidad, pruebas de credenciales y otros flujos de trabajo en los que las empresas necesitan actuar sobre hechos en lugar de afirmaciones.

A medida que esos hechos se acumulan, Verona se convierte en algo más que una capa de verificación. Se convierte en una capa de inteligencia autorizada, donde empresas y agentes computan sobre hechos respaldados por fuentes y consentidos por usuarios para tomar mejores decisiones sin exponer los datos subyacentes. Un prestamista puede razonar sobre ingresos verificados. Una aseguradora puede razonar sobre elegibilidad o eventos verificados. Un marketplace puede razonar sobre reputación o historial de transacciones verificados. Y un equipo de estrategia por fin puede entender el comportamiento del mercado a partir de hechos autorizados en lugar de paneles obsoletos y datos inferidos.

La inteligencia de mercado heredada se construye sobre aproximaciones: paneles, encuestas, brokers, señales raspadas, conjuntos de datos obsoletos y comportamiento inferido. Verona reemplaza esas aproximaciones con cómputo autorizado sobre hechos verificados. El usuario mantiene el control, los datos brutos permanecen privados, y la salida es algo en lo que una aplicación, empresa o agente puede confiar.

Esta es la dirección más amplia de la red: datos verificados como infraestructura económica, y Verona como el lugar al que acuden aplicaciones, empresas y agentes cuando necesitan saber que algo es verdadero.

## 7 Token

Verona tiene un token de red, VERONA, y una unidad de liquidación estable, una stablecoin nativa. Cumplen funciones distintas.

VERONA es el token nativo de red que impulsa la capa de inteligencia para la IA. Paga la ejecución bajo la superficie, coordina el acceso y la seguridad, y acumula valor a partir de la actividad verificada, es decir, ingresos, en la red. La stablecoin nativa es la unidad que usuarios, aplicaciones, agentes y empresas usan para pagar, recibir y liquidar en una denominación estable.

Aplicaciones, agentes y empresas pueden interactuar mediante rieles familiares como fiat o stablecoin. Debajo de esa experiencia, la actividad verificada crea ingresos de red, y los ingresos de red crean demanda de VERONA, lo que a su vez aumenta la seguridad de la red.

### 7.1 Una red que genera valor

Verona recibe una parte de los ingresos de los casos de uso y aplicaciones que impulsa, porque la red tiene éxito cuando esas aplicaciones tienen éxito, y no gana nada con el spam. Verona genera ingresos hoy, y esos ingresos contribuirán a una recompra y quema programática. El valor y la seguridad de la red se acumulan a partir de actividad económica real, no de contar transacciones.

### 7.2 Fuentes de ingresos

Verona genera ingresos hoy y está diseñada para expandir sus superficies de ingresos a medida que la red crece entre aplicaciones, agentes, marketplaces y productos de inteligencia autorizada. Los ingresos se aportan a la recompra y quema programática del token.

- Participación en ingresos de aplicaciones del ecosistema. Un porcentaje predefinido de los ingresos de un proyecto se toma como tarifa de plataforma. Estas tarifas son un contribuyente importante para asegurar la seguridad y perfeccionar la inteligencia de Verona Network.

- Tarifas de verificación. Aplicaciones, agentes y empresas pueden pagar para verificar hechos en la fuente, comprobar predicados, actualizar hechos que necesitan estado vigente, verificar procedencia o confirmar validez y estado de revocación. Esto incluye zkTLS para una sesión web, zkEmail para correo electrónico, zkPassport para identidad, una App o Enclave Attestation para una aplicación, una Photo Attestation para una imagen real y auténtica, y zkDCap para una prueba en cadena de una atestación de hardware.

- Contratos empresariales. Pagos directos de marcas y empresas por infraestructura de verificación: prevención de fraude, segmentación verificada, verificación de usuarios e infraestructura de fidelización.

- Tarifas por reutilización de datos verificados. Un hecho verificado una vez puede ser reutilizado por destinatarios autorizados. La reutilización puede generar tarifas cuando un hecho verificado se presenta, transforma, vuelve a cifrar, actualiza o delega a un agente.

- Tarifas de interacción de agentes y liquidación de acciones. Los agentes realizan acciones en cadena para operar en nombre de un usuario. A medida que los agentes transaccionan sobre datos verificados, la liquidación genera tarifas. Los agentes pueden actuar sobre hechos verificados, cambiar permisos, liquidar pagos, presentar credenciales y transaccionar con servicios u otros agentes.

- Inteligencia empresarial y cómputo privado. Las empresas pagan para computar sobre hechos autorizados sin verlos nunca en claro. El cómputo se ejecuta dentro de un entorno de ejecución confiable (TEE), de modo que los hechos subyacentes nunca salen del control del usuario y el propio cálculo es verificable. La empresa obtiene inteligencia a la que no podría acceder de otro modo, mientras los usuarios conservan la propiedad de los datos de los que se deriva. Las cargas de trabajo pueden incluir consultas agregadas, puntuación verificada, decisiones de elegibilidad, modelos de fraude, derivaciones entre fuentes e inteligencia permanente que se actualiza a medida que cambian los hechos.

- Retornos de reservas de liquidación. La capa de liquidación estable de la red está totalmente respaldada, y los retornos generados por esas reservas pueden contribuir a los ingresos de la red, cuando estén disponibles y sean legalmente permisibles.

### 7.3 La stablecoin nativa

Verona tendrá un dólar digital uno a uno, totalmente respaldado, de un emisor regulado. Lleva retornos generados por reservas bajo una estructura escalonada. Los agentes pueden usarlo para pagar verificación, acceso a pruebas, cómputo y acciones completadas. Las empresas pueden usarlo para pagar flujos de verificación e inteligencia autorizada. Los usuarios pueden recibir beneficios estables cuando los hechos que autorizan crean valor económico.

### 7.4 Cómo se acumula el valor y aumenta la seguridad de la red

Los ingresos de red financian recompras y quemas. Verona no depende de emisiones perpetuas como su motor económico central.

![](../../.gitbook/assets/litepaper-image10.png)

Los tokens se recompran con ingresos y se aportan a la quema programática. A medida que los ingresos crecen hasta cubrir el costo de operar la red, cae la parte inflacionaria de las recompensas.

## 8 Conclusión

La IA se vuelve inteligente cuando puede actuar sobre lo que es verdadero. Eso requiere hechos verificados que sean propiedad de las personas a las que describen, reutilizables por cualquier agente y no expuestos a nadie. Verona es la red donde viven esos hechos y la capa sobre la que se construye la economía agéntica. Verifica una vez, reutiliza en todas partes, no expongas nada. Hacer que la IA sea inteligente.

## Referencias

D. Boneh and M. Franklin. Identity-Based Encryption from the Weil Pairing. CRYPTO 2001.

DomainKeys Identified Mail (DKIM) Signatures. RFC 6376.

The Transport Layer Security (TLS) Protocol Version 1.3. RFC 8446.

Intel Trust Domain Extensions (Intel TDX). Intel Corporation.

---

Este documento es solo para información general. No constituye asesoramiento de inversión, ni una oferta de venta ni una solicitud de compra de ningún token u otro instrumento. No debe utilizarse como base para decisiones legales, fiscales o contables. Verona está en desarrollo activo, y cualquier función descrita aquí puede cambiar. Las declaraciones sobre el futuro están sujetas a cambios sin previo aviso. La stablecoin es un dólar digital regulado y totalmente respaldado; cualquier retorno generado por reservas es discrecional y depende del desempeño de las reservas.
