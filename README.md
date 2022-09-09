# Homologacion FiberHome Tigo Une

	Equipo de pruebas
		cpeid: FHTT9B996728 o FHTT9B991C60 Version: RT2821__WKE2.134.321B9G__1.0
	Equipo Huawey
		cpeid: 48575443CE6E0B9F Version: V5R019C00S122__15AD.A__1.0	

## Policy Manager
---

	Las politicas se ejecutan por prioridad y aplica de acuerdo al evento que traiga el CPE y como este configurado la politica

* ### **policies.initialize_cpe:**
	Se crea la carpeta /live/AXDeviceLib/devices/FiberHome/WKE2.134.321B9G/RT2821 con lo siguientes Script

	1. **basic_configure** ➪ Se crea el basic configure que contiene la configuracion inicial del dispositivo cuando llega al ACS
	2. **toip_configure** ➪ Se crea el script, este service para el aprovisionamiento de Toip ver en la seccion de escenarios
	3. **wlan_configure** ➪ Se crea el script, este service para el aprovisionamiento de Wlan como el cpe no soporta wlan, se retorna solamente un mensaje

* ### **policies.reprovision_cpe:**
  Politica encargada de ejecutar el aprovisionamiento  de los servicios del SS cuando el equipo llega atraves de Bootstrap(Factory Reset).
	
	No se modifica nada pero se debe modificar cada uno de los servicios, ver la seccion de aprovisionamiento de servicios

* ### **policies.firmware_handling:**  
	politica encargada de verificar si el firmware del dispositivo esta habilitada, necesita actualizacion o esta OK

* ### **policies.firmware_upgrade:**  
	Politica encargada de lanzar la actualización del dispositivo [boot, bootstrap(fr), periodic]

---
## Portal de Soporte
	La configuracion del portal de soporte se encuentra  /live/AXSupportPortal/Configuration/ConfigFile/DiagnoseParams. 
	En tigo une se tiene un DiagnoseParams para todos los dispositivos, se decide mejor crear uno solo para el fiberHome.
### **Archivo de Configuracion DiagnoseParams**
  * **getCustomConfigFile**  Script encargado de decidir cual es el archivo de configuracion del portal de soporte. Se modifica
  * **FiberHome** Se crea el archivo que contiene la configuracion para este modelo. Aca se cambia los parametros TR para el dispositivo

### **Custom Match**
En estos se encuentran los scripts que le dicen al diagnose params si la opcion se debe mostrar para ese dispositivo. /live/AXSupportPortal/Configuration/CustomMatch/

* match_fiberHome ➪ Nuevo
* match_vlan_1 ➪ modificado
* match_toip ➪ modificado
* mat_toip_wcd_4 ➪ Modificado

### **Escenarios**
	Los escenarios Reboot, Factory_reset, Obtener parametros, configurar parametros, Actualizacion de firmware son Escenarios estandar por lo cual no hay que hacer modificaciones
	
* **Cambiar direccion IP LAN**   
/live/CPEManager/Scenarios/AXSupport/UNE_ChangeIPLan_2 ➪ modificado

* **Configurar usuario y contraseña de internet**:  
/live/CPEManager/Scenarios/AXSupport/get_InternetPVC ➪ modificado

* **Ingresar manualmente usuario y contrasena de Internet:**  
	/live/CPEManager/Scenarios/AXSupport/get_InternetPVC ➪ modificado
* **Modificar rango DHCP** No hubo la necesidad de realizar cambios en el script changeDHCPPoolConfig

* **Configuracion y Visualizacion DMZ**:      /live/CPEManager/Scenarios/AXSupport/ShowDMZConfiguration ➪ Modificado   /live/CPEManager/Scenarios/AXSupport/DMZ_Config/ShowDMZConfigurationFiberHome ➪ Creado

   

  ~~~
		InternetGatewayDevice.X_FH_DMZ.NumberOfRule : 1
		InternetGatewayDevice.X_FH_DMZ.Rule.1.Wanname : INTERNET_R_VID_200
		InternetGatewayDevice.X_FH_DMZ.Rule.1.hostip : 192.168.1.4
	~~~
	1. set parameter InternetGatewayDevice.X_FH_DMZ.NumberOfRule : 1
	2. {'ParameterKey': '', 'ObjectName': 'InternetGatewayDevice.X_FH_DMZ.Rule.'}   Agregar un objeto, como son parametros propietarios es necesario agregarlos en el XML
	3. set parameter Wanname y hostip

	

	* **Configuracion DMZ**:  
	/live/CPEManager/Scenarios/AXSupport/DMZConfiguration ➪ Modificado
	/live/CPEManager/Scenarios/AXSupport/DMZ_Config/DMZConfigurationFiberHome ➪ Creado

	* **Visualizacion DMZ**:  
	/live/CPEManager/Scenarios/AXSupport/ShowDMZConfiguration ➪ Modificado
	/live/CPEManager/Scenarios/AXSupport/DMZ_Config/ShowDMZConfigurationFiberHome ➪ Creado

	
* **Configurar conexion para marcado PPP desde el PC en puerto 1**  
	/live/CPEManager/Scenarios/AXSupport/UNE_PortOneBridge_2 ➪ Modificado

* **Actualizacion de Firmware**  
	Escenario estandar solo es necesario crear el download job en firmware manager

* **Liberar puertos de Gestion** N/A
* **Aprovisionamiento IPTV** N/A
* **Solicitar un PING a Internet** Escenario estandar no hubo la necesidad de modificar script
* **Cambiar configuracion WLAN** N/A
* **Configuracion de  Puertos LAN**: N/A
 * **Visualizacion de Puertos LAN**:  
  /live/CPEManager/Scenarios/AXSupport/LanPortConfiguration ➪ Modificado
	 /live/CPEManager/Scenarios/AXSupport/LANPort_Config/LanPortConfigurationFiberHome ➪ Creado 

---
## Aprovisionamiento de Servicios
	El ACS a traves del ticket manager ejecuta el aprovisionamiento de los servicios. Cada servicio tiene una carpeta creada en /live/CPEManager/Scenarios/Services y dentro de cada uno tiene los script
	activate_serviceName, deactivate_serviceName, modify_serviceName.


* **GPON** Scripts modificados.   
  * /live/AXDeviceLib/devices/pppoe_configure ➪ Archivo que llama la configuracion
  * /live/CPEManager/UNE_CONF/checkSupportedService ➪ modificado, se agrega la version del modelo y los servicios que soporta
  * /live/AXDeviceLib/logic/get_wan_ppp_connection_for_gpon ➪ modificado los parametros TR correspondientes
* **Toip** Script Modificados   
  * /live/AXDeviceLib/devices/FiberHome/WKE2.134.321B9G/RT2821/toip_configure ➪ modificado los parametros TR correspondientes, validar lo del puerto con tigo Une
* **AndroidTV**: Se va guardar el servicio como IPTV pero el puerto aparece en 0 y no se hara ninguna configuracion en el dispositivo ya que este los tiene mapeados por defecto.
 /live/AXDeviceLib/devices/FiberHome/iptv_configure ➪ modificado
---
## Modulo Firmware Manager
	El modulo de firmware manager se encuentra en /live/CPEManager/AXFirmwareManager
* **Managed Firmwares:** Base de datos todos los firmware que se han registrado en la plataforma, por defecto lo agrega y lo coloca deshabilitado
* **Dowload Jobs:** Donde se crean los jobs para las actualizaciones de firmware, los condition script se ven en la pestaña Main.
* **Download Logs:** Donde se visualizan las actualizaciones que se han hecho  


El firmware Manager tienen 2 politicas
* **policies.firmware_handling** Se encarga de evaluar el estado del firmware del dispositivo, 3 Estados: Soportado, needUpgrade, Deshabilitado
*  **policies.firmware_upgrade** Lanzar la actualizacion que fue creada en los download jobs y cumple las condicion, 

---
## Modulo Campaign Manager
	El modulo de firmware manager se encuentra en  /live/CPEManager/AXCampaignManager

1. **Crear Filtro:** Se puede el filtro por condicion cpe, sql, campaign
2. **Crear la campaña:** En la pestaña Campaign se crea la campaña.
---

## removeService, setService, swapService  
	Estos webservice se encuentran en /live/CPEManager/Interfaces/InterfaceMethods. 
	Se crea el script Test_webservice, para realizar pruebas, donde estamos simulando el llamado del webservice. Se modifica el file /live/CPEManager/UNE_CONF/checkSupportedService donde se especifica los servicios soportados por el dispositivo
* **removeService:** Desaprovisionar el los servicios que se envian desde el webservice
* **setService:** Aprovisiona o modifica el servicio que se envia desde el webservice   /live/CPEManager/Interfaces/InterfaceMethods/setService
 ➪ modificado
* **swapService:** cambiar todos los servicios(exc TOIP) de un cpe a otro 
	
---
# Herramientas de Test y Diagnostico
Este modulo se encuentra en  /live/UneAdmin/ --> Donde los script
/live/UneAdmin/HTrD --> al dar click en view se abre el portal de HTrD

1. Añadir el modelo(I.DI.MN) en la pestaña configuration - Modelos CPE
2. Verificar cada uno de los parametros e ir comparando y si es necesario agregar el parametros especifico
3. Grupos no se modifica.

Webservice
/live/CPEManager/Interfaces_HTrD/InterfaceMethods

	/live/CPEManager/Interfaces_HTrD/InterfaceMethods/Tools/tr_scenarios ➪ modificado
	/live/CPEManager/Interfaces_HTrD/InterfaceMethods/Tools/tr_firmwares ➪ modificado
	/live/CPEManager/Interfaces_HTrD/InterfaceMethods/GetSupportedServices ➪ modificado

Escenarios
* RT2821__WKE2.134.321B9G__1.0
* UNE_ChangeIPLan_2     		A
* UNE_RoutedPPPoE       		N/A
* DMZConfiguration      		A
* DownloadFirmware      		A
* FactoryReset          		A
* Reboot                		A
* LanPortConfiguration  		N/A
* ShowLanPortConfiguration  A
* ShowDMZConfiguration      A
* UNE_DmzAsNatRule          N/A
* UNE_PortOneBridge_2				A
* UNE_FaxEnable							N/A
* UNE_ReleaseManagePorts		N/A
* IPTV_PortProvisioning     N/A
* SetPingHost								A	
* ChangeWLAN								N/A
* ShowParentalControl       N/A
* changeParentalFilter			N/A
* ParentalControl						N/A
* ChangeVoice								N/A
* VoiceInfos								N/A

---
## Notas Importantes
* Para los parametros propietarios de cada CPE que se identifican por X_, se debe crear un archivo XML y almacenarlo en /etc/axess/TR69/Properties y reiniciar los servicios.
* Obtener el cpe
  ~~~ 
	r = container.REQUEST
	cm = r['CM']
	cpe = cm.manage_getCPEObjByID(r,'FHTT9B996728')
  ~~~

* Script creados de ayuda  
  * /live/AXDeviceLib/call_project_specific_pruebas ➪ Script de pruebas para identifica el script que va ejecutar
  * /live/CPEManager/Scenarios/policies/copy_of_reprovision_cpe ➪ Script de prueba para lanzar el reaprovisionamiento de servicios, se debe eliminar


## Portmaping
	1. Se deben agregar los parametros propietarios en el XML
	2. add object
	{'ObjectName': 'InternetGatewayDevice.WANDevice.1.WANConnectionDevice.1.WANPPPConnection.1.PortMapping.', 'ParameterKey': ''}
	3. Luego Setear los parametros con el Set paramater values
