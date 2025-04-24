**Вкратце:**
Ребятам необходимо удалить, а затем заново добавить device и things. В dashboard просто по новой привязать переменные

# Теория

    Цели занятия:
    1. Научится работать в arduino cloud
    2. Сделать приложение по сбору данных с датчика dht11

# Практика

1. Переходим на https://cloud.arduino.cc/ и нажимаем get started for free

Затем регистрируемся(Не забываем нажать register(маленькая ссылка внизу)) при помощи почты

2. После регистрации заходим во вкладку `devices` и выбираем `thirdparty device`
   ![|490x256](./images/занятие%20cloud%20arduino%20iot-1744898017965.png)

`Обязательно!!!` загружаем pdf и завершаем добавление устройства

3. Затем переходим во вкладку `things`

Там нам нужно будет добавить так называемые `облачные переменные` следующих типов(то что выделено голубеньким и после слова Cloud) и соответствующих названий(их желательно повторить 1 в 1, чтобы код указанный ниже вам не пришлось исправлять):
![|338x277](./images/занятие%20cloud%20arduino%20iot-1745479775828.png)

Для значений с датчика (влажности и температуры) нужно выбрать такие настройки. Можно поменять частоту отправки данных. Допустим не 3 секунды, а 2 или 5
![|324x521](./images/занятие%20cloud%20arduino%20iot-1745478561990.png)

4. Также находясь в этой же панели things, необходимо сразу же подключить(`Associate`) нашу плату(`device`) и указать параметры wifi сети, через которую она будет подключаться к серверам Arduino IOT.

Здесь нам и понадобится тот самый `секретный ключ из pdf`

По итогу ваша правая часть подкладки `Setup` должна выглядеть примерно таким образом. Разве что статуса `Online` пока что не будет, так как мы пока что не загрузили `Sketch`
![|348x398](./images/занятие%20cloud%20arduino%20iot-1745479289930.png)

5. Затем переходим во вкладку `Dashboards` и выбираем `create new dashboard`.

Нас закидывает в пустое поле, чтобы редактировать панель элементов нужно нажать кнопку edit и начать добавлять и настраивать их.

Влажность это виджет `percentage`, температура - `gauge`, график - `chart`, светодиод - `switch`. И привязываем к виджетам соответствующие им облачные переменные
На данном этапе также можно адаптировать приложение под мобильный вид
![|439x453](./images/занятие%20cloud%20arduino%20iot-1744898489695.png)

6. Переходим обратно в `things` и переходим вкладку `sketch` и первым делом подключаем библиотеку
   ![|332x476](./images/занятие%20cloud%20arduino%20iot-1745478867354.png)

После нажатия кнопки `include`, в самом верху скетча у вас должны автоматически появиться эти 2 строчки
![|344x198](./images/занятие%20cloud%20arduino%20iot-1745478945607.png)

Затем пишем примерно такой код.

```cpp
#include <DHT.h>
#include <DHT_U.h>

#include "thingProperties.h"
#define DHTPIN 33
#define DHTTYPE DHT11
DHT dht(DHTPIN, DHTTYPE);
void setup() {
  // Initialize serial and wait for port to open:
  Serial.begin(115200);
  // This delay gives the chance to wait for a Serial Monitor without blocking if none is found
  delay(1500);
  pinMode(2,OUTPUT);
  dht.begin();
  // Defined in thingProperties.h
  initProperties();

  // Connect to Arduino IoT Cloud
  ArduinoCloud.begin(ArduinoIoTPreferredConnection);

  setDebugMessageLevel(2);
  ArduinoCloud.printDebugInfo();
}

void loop() {
  ArduinoCloud.update();
  // Your code here
  DHT_SENSOR_READ();

}
/*
  Since Led is READ_WRITE variable, onLedChange() is
  executed every time a new value is received from IoT Cloud.
*/
void onLedChange()  {
  // Add your code here to act upon Led change
    if(led == 1){
    digitalWrite(2,HIGH);
  }
  else{
    digitalWrite(2,LOW);
  }
}
void DHT_SENSOR_READ(){
  float h = dht.readHumidity();
  // Read temperature as Celsius (the default)
  float t = dht.readTemperature();
  humidity = h;
  temperature = t;
  Serial.print(F("Humidity: "));
  Serial.println(h);
  Serial.print(F("%  Temperature: "));
  Serial.println(t);
}
```

Для возможности загрузки кода на плату необходимо установить агента ардуино по ссылке
https://cloud.arduino.cc/download-agent

После установки агента можно загружать скетч на плату. Лично мне для успешной загрузки приходится зажимать кнопку `BOOT` в момент когда появляется упоминание COM порта
![](./images/занятие%20cloud%20arduino%20iot-1745479034064.png)

# Источники инфы

https://www.youtube.com/watch?v=rcCxGcRwCVk&t=835s&ab_channel=techiesms
