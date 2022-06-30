# SmartPlantCareDevice

Проект за предметот Софтвер за вградливи системи.

За имплементирање на проектот се користи Spring Boot веб апликација која служи како online база за уредот и .secrets.h датотека во која се запишуваат броевите на пиновите на периферните уреди, ssid и лозинката и URI за серверот кој служи како база.

Кодот за веб апликацијата може да се најде на следниот линк https://github.com/MartinRudnikovski/PlantCareDeviceDataBase.

### Resources
Тука се имплементирани сензорите и актуаторите како класи. Идејата е да бидат интерфејс за секој индивидуален периферен уред, при што секоја инстанца има свој mutex и единствен јавен метод за работа со уредот кој е имплементиран да биде thread-safe благодарение на семафорот.

### Tasks
Тука се имплементирани тасковите кои мора рачно да се дефинираат (не се креираат од дополнителна библиотека). Секој таск потребно е да се нагласи да работи на јадро 1, затоа што WiFi таскот кој се користи во позадина работи со јадро 0, па не би сакале да дозволиме да се борат тасковите за тој ресурс.

Од realTimeSensors.h единствено е потребно да се повика функцијата vStartRealTimeSensorsTask(SoilMoistureResource &soil, DhtResource &dht) која го креира таскот за вчитување од сензорите и да ги прикаже на LCD. Исто така, периодично се прикажува или локалната IP адреса на уредот или дека не е поврзан со WiFi мрежата.

Од sensorManagementTask.h единствено е потребно да се повика функцијата vStartSensorManagementTask(SoilMoistureResource &soil, DhtResource &dht) која го креира таскот за периодично (на 30 минути) да ги вчитува вредностите на сензорите и да ги препрати до серверот кој служи како база.

### Web
Тука се дефинира корисничкиот интерфејс до кој се пристапува преку IP адресата прикажана на LCD во realTimeSensors.h. За подобра евиденција на сите променливи, дефинирани се повеќе namespace-ови.

_variables : Тука се дефинирани променливите кои се користат во мапирањта на барањата.

_html : Тука се дефинирани HTML темплејти како rawliteral-String-ови. Со цел адаптивно променување на некои вредности во HTML-овите, се користат placeholder функции кои заменуваат вредностите во rawliteral-string-овите според имињата во две процент знаменца. (%<name>%)

_placeholders : Тука се дефинирани placeholder функциите кои се спомнати во објаснувањето на _html. Тие треба да имаат форма од типот String placeholder_dashboard(const String& var).
  
Со цел стартување на корисничкиот интерфејс, се користи функцијата 
vStartInterface(SoilMoistureResource &soil, DhtResource &dht, WaterPump &pump), која ги конфигурира сите параметри потребни за да се започне со опслужување на барањата. Главната страница е мапирана на "/" и од таму се навигира до сите наредби преку копчиња.
  
/today : Оваа патека се користи за добивање на анализата за просечните вредности на сензорите во текот на тековниот ден. Се пристапува до серверот кој служи како база за добивање на овие информации. Тие стигаат како Json string и се користи ArduinoJson.h за да се десеријализираат за поматамошна употреба.

/month : Слично како /today, но вредностите за тековниот месец.

/schedule : Им овозможува на корисниците да наместат распоред за наводнување на растенијата. Може да се избере на колку дена да се наводнува, па и за толку време би било првото наводнување. Се користи /done за потврдување на оваа одлука.

/water : Се користи за веднаш да се наводени растението и ова трае 3 секунди. Со цел да има брз одзив, се креира нов таск кој го врши ова и се брише таскот веднаш отпосле.

Библиотеката која се користи за имплементацијата на веб серверот е ESPAsyncWebServer.h за која може дополнително да се најдат ифнормации на официјалната документација https://github.com/me-no-dev/ESPAsyncWebServer или од мојата трета проектна задача на курсот.

Библиотеката која се користи за испраќање на HTTP пораки е HTTPClient.h библиотеката за која може да се видат неколку примери на официјалниот GitHub линк https://github.com/espressif/arduino-esp32/tree/master/libraries/HTTPClient или од мојата втора проектна задача на курсот.
