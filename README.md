# PreferencesESP32StructArray
Пример  работы с библиотекой Preferences для  esp32 стурктурами и массивами.


/*
Автор: Дмитрий Вительевич Мельник
Почта: urbannova@yandex.ru
Описание: Пример  работы  с библиотекой Preferences для esp32 для  хранения структур и массивов.
Так как сама библиотека не поддерживает  хранение массивов, можно обойти это ограниение  создавая структуры  массивов и  записывая в память их.
Так можно обеспечить запись большого числа данных, а  так же например  записать в  структу сотни  параметров, так как пространство имен Preferences
ограничено  15 именами.
Пример довольно простой, рабочий, и понятный, просто  вырежу  лишние из примера для своего кода.
По благодарить можете 410017505703275 ЮМоней.
*/

#include <Preferences.h>
Добавляем библиотеку
Preferences prefs;
Создаем объект класса Preferences

typedef struct {
  uint32_t CANID;//Адрес в кан шине
  uint8_t CANPOZ;//Позиция в кан шине
  uint8_t CANPOL;//Сколько  взять  байт
} can_struct;
Создаем структуру
can_struct can[16];
Объявляем массив структур, типо как массивы
unsigned int counter;

void setup() {
  Serial.begin(115200);
  prefs.begin("can"); Создаем ключ
  prefs.begin("canspeed"); Создаем ключ
  counter = prefs.getUInt("canspeed", 0); даем  стартовую иницилизацию если ее не было
  prefs.getUInt("canspeed", counter); Получаем данные если то то  есть
  Serial.printf("Скорость из памяти %d \n", counter); Выводим данные если что то есть
  esp_read(); Смотри  описание  функции
  Serial.printf("Число из памяти %d \n", can[0].CANPOZ); выводим информацию из структуры

}

void loop() {
  can[0].CANPOZ = can[0].CANPOZ + 1;
  esp_write();
  delay(5000);
  esp_read();
  Serial.println(can[0].CANPOZ);
  counter = can[0].CANPOZ;
  prefs.putUInt("canspeed", counter);
}

void esp_read(){ Мы  читаем структуру  как последовательность  байт для простоты  работы
  size_t schLen = prefs.getBytesLength("can");
  if (0 == schLen || schLen % sizeof(can_struct) || schLen > sizeof(can)) {
    Serial.printf("Invalid size of schedule array: %zu\n", schLen);
    return;
  }
  prefs.getBytes("can", can, schLen);
}

void esp_write(){ записываем структу в виде байт
  prefs.putBytes("can", can, sizeof(can));
}
