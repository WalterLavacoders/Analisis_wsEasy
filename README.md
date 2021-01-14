#  Analisis de wsEasy  #

* Configura ws local y ws cloud (busca archivo data.sac o configuracion manual)
* inicializa conexion de base de datos (Estan hardcodeados en ```connection/DadosConexao.java```)
* Inicia configuracion de la aplicacion (toma los datos de la tabla ```config_ap```)
* Inicia configuracion de los controladores (select de tabla ```config_controller``` relacionada con ```config_ap```,```brinquedo_leitor```,```brinquedo```)
* una vez que termina de cargar la lista de controladores parametrizados, cierra conexion a base de datos
* inicia aplicacion, abriendo puerto correspondiente (el select actual a la base esta devolviendo 2 configuracion una en puerto ```3002``` y otra en puerto ```3003```)
>En java existe la clase **Thread**, que ejecuta hilos dentro de un mismo programa. De esta manera inicia los distintos socket que necesita.
* por cada conexion que recibe de los controladres crea un nuevo hilo de ejecucion en donde hay 3 Metodos principales.
    * **Metodo readMsg:**
        mapea los la cadena de hexadecimal a un formato { clave: valor }
    * **Metodo processMsg:** _(recibe mensaje del controlador)_. 
        mensaje que setea el controlador y actualiza el arreglo de configuraciones de controladores en la servicio
        entre otras configuraciones, aparecen las siguientes:
        ```
        - "Device Target"
        - "mesaje tipo"
        - "Seteo de frecuencia"
        - "seteo de GM timeout"
        - "seteo de grupo"
        - "seteo cada cuanto el AP, va a pedir refresh"
        - "seteo RF power"
        - "seteo de cantidad de nodos a scanear"
        - "seteo cada cuanto el AP, va a enviar un pack ethernet"
        - "seteo de RF polling a ON"
        ```
    * **Metodo processMsgRF:** _(recibe mensaje del controlador)_. 
    este metodo recibe 2 tipos de mensajes:  
        * **Tipo login:**  
            le manda toda la configuracion necesaria al controlador  
            Ejemplo de lo que se puede configurar:  
            ```
            - "veo cuantos botones debo mostrar"
            - "si es multiprice o hay que mostrar propagandas"
            - "el bit 2 de este byte indica si fuerza o no real tickets con la entrada de un pulso por A"
            - "agrego inputs inverted y forcerealtickets"
            - "tomo la forma de onda del pulso"
            ```
        * **Tipo pass Card:**  
            este formatea el mensaje dependiendo si es credito, tiempo, tickeT, entre otras cosas mas y arma parametros para ejecutar un post a los siguienetes WS:
            ```
            dadosConexao = new DadosConexao();
            dadosConexao.serverName = "localhost";
            dadosConexao.dataBase = "sacoacloud";
            dadosConexao.username = "USER";
            dadosConexao.password = "xxxxxxxx";
            dadosConexao.URL_WS = "https://192.168.0.115/sacoacloudsystem/webservices/ws_controladora.php";

            DadosConexao.listConexoes.put("local", dadosConexao);

            dadosConexao = new DadosConexao();
            dadosConexao.serverName = "azuvsrveasyapp.eastus2.cloudapp.azure.com";
            dadosConexao.dataBase = "sacoacloudsystem";
            dadosConexao.username = "USER";
            dadosConexao.password = "xxxxxxxx";
            dadosConexao.URL_WS = "https://www.rajsolucoes.com.br/sacoacloudsystem/webservices/ws_controladora.php";

            DadosConexao.listConexoes.put("sacoacloudsystem", dadosConexao);
            ```
            **una vez que el ws retorna resultado, parsea el mensaje y este es devuelto al controlador mediante socket.**

_El resto de los metodos del codigo son para crear encabezados de mensajes y creacion de labels entro otros._

