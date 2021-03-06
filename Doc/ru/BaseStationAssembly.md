# Сборка станции отметки

### Компоненты станции отметки

![](/Images/s01.jpg?raw=true)

1.	RFID-модуль RC522.

2.	Печатная плата. Можно заказать через EasyEDA. Gerber файлы находятся в папке [Проект]\hardware\BaseStation\prod (на фото устаревшая версия печатной платы, не обращайте внимания)

3.	Микросхемы

a.	Микроконтроллер Atmega328pAU 

b.	Часы DS3231SN 

c.	Стабилизатор напряжения MCP1700T-33. 

d. 	Транзистор BSS138 в корпусе SOT-23.

4.	Пассивные компоненты в формате 0805. 

a.	Резистор 100 Ом = 1 шт.

b.	Резистор 3,3 кОм = 3 шт.

c.  Резистор 33 кОм = 2 шт.

d.	Конденсатор 0.1 мкФ = 4 шт.

e.	Конденсатор 1 мкФ = 2 шт.

f.  Индуктивность 2.2 мкГн, ток насыщения > 500mA (например LQH32MN2R2K) = 2 шт. (Не обязательно)

5.	Выводные компоненты

a.	Разъём PLS-8 на плату (или другими словами "штырьки" идут в комплекте с RFID-платой)

b.	Два разъёма SIP-6 на плату (можно сломать из "гребёнки" SIP-40)

c.	Светодиод 3мм.

d.	Пищалка TR1205Y или HC0903A.

6.	Батарейный отсек 3xAA или 3xAAA (тогда корпус будет закрываться проще)

7.	Корпус Gainta G1020BF. Хороший корпус из ABS пластика, неплохо поддается допиливанию под свои цели.


Оборудование, которое пригодится при сборке, приведено [на отдельной странице](/Doc/ru/Equipment.md)

### Подготовка RFID-модуля

Сборку станции отметки лучше начать с подготовки RFID-модуля. У него необходимо аккуратно выпаять светодиод, резистор R1 и подтягивающий к линии RST резистор R7, иначе всё это будет сильно потреблять ток во время работы устройства. Отпаянный резистор R1 на 100 Ом и светодиод можно пустить дальше в дело.

Также можно заменить индуктивности L1 и L2 на аналогичные 2.2 мкГн, но с большим рабочим током для увеличения мощности антены, например Murata LQH32MN2R2K (Спасибо за совет Александру Якимову).

![](/Images/s12_1.JPG?raw=true)

### Пайка основной платы

![](/hardware/BaseStation/prod/sportiduino-base-scheme.jpg?raw=true "Принципиальная схема")

![](/hardware/BaseStation/prod/sportiduino-base-assembly.jpg?raw=true "Расположение компонентов")

Если нет опыта пайки мелких компонентов, хорошо бы сначала потренироваться на какой-нибудь ненужной плате. Отпаивать-припаивать разные детали. Можно посмотреть ролики на ютубе или ещё где-нибудь, информации довольно много. Промазываем флюсом все места будущей пайки. Флюс особо лучше не жалеть. Далее припаиваем микросхемы. После микросхем припаиваем транзистор, стабилизатор напряжения, резисторы и конденсаторы. Резистор R7 на 3,3 кОм можно не припаивать, так как он сейчас не выполняет никаких функций и зарезервирован для будущих версий. В конце припаиваем светодиод, пищалку, "штырьки" и батарейный отсек. Светодиод припаиваем с обратной стороны платы. При этом, помните мы отпаивали от RFID-модуля светодиод и резистор на 100 Ом, здесь мы можем их использовать. По желанию, можно аккурантно припаять SMD-светодиод от RFID-модуля на нашу печатную плату вместо 3мм-светодиода. Модуль RC-522 пока не припаиваем! 

**Внимание:** не перепутайте при пайке полярность у светодиода и пищалки!

![](/Images/BaseStationSoldedTop.jpg?raw=true)

![](/Images/BaseStationSoldedBottom.jpg?raw=true)

### Программатор

Для прошивки микроконтроллера потребуется программатор. Если у вас нет программатора, то его можно сделать из Arduino Nano. Для этого надо всего лишь припаять шесть проводов длиной 25см к плате Arduino Nano, а с другой стороны к разъёму SIP-6. Далее будет описан процесс прошивки при помощи программатора из Arduino Nano.

Собираем программатор из Arduino Nano по схеме ниже, но пока не впаиваем конденсатор 10 мкФ*16В.

![](/Images/ArduinoNanoIspScheme.png?raw=true)

Далее подключаем его к ПК и запускаем Arduino IDE. Открываем скетч ArduinoISP из примеров Arduino (Файл->Примеры->ArduinoISP). Далее в меню Инструменты выбираем плату Arduino Nano, процессор Atmega328p (Old Bootloader) и Порт.

![](/Images/ArduinoIspScr1.jpg?raw=true)

Выполняем Скетч->Загрузка. После этого припаиваем конденсатор на 10 мкФ*16В между GND и RST как на схеме выше. Если не припаять этот конденсатор, то при программировании постоянно будет вылетать ошибка: avrdude: stk500_getsync() attempt 1 of 10: not in sync: resp=0x15. Программатор готов! 

![](/Images/ArduinoNanoISP.jpg?raw=true)

### Настройка Arduino IDE

В меню Файл->Настройки изменяем размещение папки скетчей на [Проект]/firmware.
Или можно скопировать содержимое папки firmware в папку скечей, указанную в настройках.

Далее нам необходимо создать описание нашей платы Sportiduino. Для этого открываем файл [Program Files]\Arduino\hardware\arduino\avr\boards.txt и добавляем в конец файла код

```
##############################################################

sportiduino.name=Sportiduino

sportiduino.build.mcu=atmega328p
sportiduino.build.f_cpu=8000000L
sportiduino.build.board=AVR_SPORTIDUINO
sportiduino.build.core=arduino
sportiduino.build.variant=standard

sportiduino.upload.tool=avrdude
sportiduino.upload.protocol=arduino
sportiduino.upload.maximum_size=32256
sportiduino.upload.maximum_data_size=2048
sportiduino.upload.speed=38400

sportiduino.bootloader.tool=avrdude
sportiduino.bootloader.low_fuses=0xE2
sportiduino.bootloader.high_fuses=0xDE
sportiduino.bootloader.extended_fuses=0xFF
sportiduino.bootloader.unlock_bits=0x3F
sportiduino.bootloader.lock_bits=0x0F

menu.bootloader=Bootloader

sportiduino.menu.bootloader.optiboot=Optiboot 38400
sportiduino.menu.bootloader.optiboot.bootloader.file=optiboot/optiboot8_38400_sportiduino.hex

sportiduino.menu.bootloader.optiboot_led=Optiboot 38400 with LED
sportiduino.menu.bootloader.optiboot_led.bootloader.file=optiboot/optiboot8_38400_sportiduino_led.hex

############################################################## 
```
После этого копируем файлы \*.hex из папки [Проект]\firmware\Optiboot в папку [Program Files]\Arduino\hardware\arduino\avr\bootloaders\optiboot

Перезапускаем Arduino IDE. В меню Инструменты->Плата должна появиться наша плата Sportiduino. 

### Прошивка микроконтроллера

В Arduino IDE открываем скетч BaseStation (Файл->Папка со скетчами->BaseStation). В меню Инструменты->Плата устанавливаем Sportiduino. Далее в меню Инструменты->Программатор выбираем Arduino As ISP. 

![](/Images/BaseStationProgConf.jpg?raw=true)

Подключаем наш программатор к плате Sportiduino (разъём X3, сигнал RST должен быть на первом контакте).

![](/Images/ProgrammerConnect.jpg?raw=true)

Выполняем Инструменты->Записать загрузчик. Внимательно читаем лог. Там не должно быть сообщений об ошибке. Если что-то пошло не так, то проверяем сборку программатора и его подключение к плате, проверяем сборку самой платы. Если всё прошло успешно, то отключаем программатор, вставляем батарейки и подключаем преобразователь [USB-to-TTL](https://ru.aliexpress.com/item/32294938771.html?spm=a2g0v.search0104.3.72.3074220cAo4Mco&ws_ab_test=searchweb0_0%2Csearchweb201602_3_10065_10068_319_317_10696_453_10084_454_10083_10618_10307_10301_537_536_10059_10884_10887_321_322_10915_10103_10914_10911_10910%2Csearchweb201603_52%2CppcSwitch_0&algo_expid=60eadde8-7142-4cf0-a8a6-ed0a9acfa548-10&algo_pvid=60eadde8-7142-4cf0-a8a6-ed0a9acfa548) к разъёму X4. У преобразователя обязательно должен быть сигнал DTR. Для подключения ещё необходимо спаять простой жгут Pin-to-Pin на 6 контактов. С одной стороны розетка BLS-6 с другой SIP-6. Далее необходимо разрезать по середине провод DTR и припаять конденсатор на 100 нФ.

![](/Images/UsbToTtl.jpg?raw=true)

![](/Images/ProgrammerWire.jpg?raw=true)

![](/Images/SerialProgConnect.jpg?raw=true)

Выполняем Скетч->Загрузить. Если всё хорошо, то после завершения станция должна три раза пикнуть (что говорит о том, что часы работают) и через 5 секунд ещё раз продолжительно пикнуть. Если всё так, то можно приступать к спайке с RFID-модулем. Если нет, то необходимо всё проверить ещё раз.

### Пайка RFID-модуля и проверка

После прошивки микроконтроллера припаиваем RFID-модуль к нашей плате.

![](/Images/BaseStationRfidSolded.jpg?raw=true) 

Затем тестистируем работу станции. Предварительно стоит записать мастер-чипы номера станции, обновления времени и пару обычных чипов. После вставления батареек станция три раза пикнет, что говорит о том, что часы выставлены не верно. Затем через несколько секунд будет длинный сигнал, сигнализирующий, что программа вошла в цикл работы. По умолчанию станция работает в режиме ожидания и проверяет наличие чипа в своей зоне через каждую секунду. Приложите мастер-чип номера станции. Станция запишет новый номер и теперь уже способна реагировать на обычные чипы. Далее необходимо настроить часы. В рабочем цикле подносим мастер-чип времени и после перезагрузки подносим второй обычный чип. Считываем его на станции сопряжения и убеждаемся в корректной работе.

![](/Images/BaseStationTestAssembly.jpg?raw=true)

Если всё так, то станцию можно отмывать, например, в ванне с изопропиловым спиртом или специальным средством FluxOff. Если плата не работает, то нужно поискать возможные ошибки, прежде всего в спайке с RFID-модулем.

![](/Images/BaseStationClean.jpg?raw=true)

Мощность антены по-умолчанию 48 дБ, в некоторых станциях данная мощность избыточна и приводит к тому, что чипы перестают отмечаться при поднесении их вплотную. Для станций с этой проблемой нужно постепенно уменьшать мощность до достижения оптимального срабатывания. Для этого в SportiduinoPQ пробуем уменьшить мощность (как это сделать написано в [руководстве](/Doc/ru/UserManual.md))

### Установка в корпус

В корпусе сначала нужно просверлить отверстие для светодиода 3мм. Координаты отверсия показаны на рисунке ниже. Чтобы влага не попадала внутрь через это отверстие, его необходимо заклеить с обратной стороны скотчем, а с другой стороны залить термоклеем.

![](/Images/BaseStationHole.jpg?raw=true)

Далее необходимо приклеить батарейный отсек к нижней крышке корпуса суперклеем.

![](/Images/BaseStationBatHolderFixed.jpg?raw=true)

После этого приступаем к установке платы. Плата ложится в корпус немного под углом. Положите её так, чтобы светодиод попадал в центр просверленного отверстия, а буззер касался стенки корпуса. Далее закрепите плату термоклеем.

![](/Images/BaseStationPcbGlue.jpg?raw=true)

![](/Images/BaseStationPcbInBox.jpg?raw=true)

После этого можно залить корпус компаундом. В принципе, можно обойтись и без него. Например, если промазать стык корпуса силиконовым герметиком. Либо вообще приклеивать крышку к корпусу и делать корпус одноразовым на срок работы батарей (порядка года) и менять батареи вместе с корпусом, что может быть вполне оправданным в виду его небольшой стоимостью и надежностью получаемых станций.

***На фотографиях ниже устаревшая версия платы, но действия все остаются теми же!***

![](/Images/s26.jpg?raw=true)

На 1 станцию отмеряем 30 мл компаунда

![](/Images/s27.jpg?raw=true)

И 1 мл отвердителя. Перемешиваем отвердитель с компаундом.

![](/Images/s28.jpg?raw=true)

![](/Images/s29.jpg?raw=true)

И заливаем нашу плату. Должно получиться так, что все контакты залиты силиконом. Выходят только X3, X4 и пищалка.

![](/Images/s30.jpg?raw=true)

![](/Images/s31.jpg?raw=true)

Через сутки компаунд полностью затвердевает. При этом все детали видно, в случае чего компаунд можно легко расковырять и произвести ремонт.

![](/Images/s32.jpg?raw=true)

Батарейный отсек промазываем вазелином или другим густым гидрофобным маслом и вставляем батарейки.

![](/Images/s34.jpg?raw=true)

И закручиваем наш корпус. Детали лежат туго, может понадобиться небольшое усилие, чтобы закрыть корпус.

![](/Images/s35.jpg?raw=true)

![](/Images/s36.jpg?raw=true)

Печатаем на принтере наклейки из папки [Проект]\hardware\BaseStation\2d. Наклейки выполнены в программе Inkscape. Можно заказать печать наклеек на светоотражающей или светонакопительной плёнке. Приклеиваем наклейку и сверху ламинируем её скотчем (если наклейка из простой бумаги). Станция готова!

![](/Images/BaseStation1.jpg)

![](/Images/BaseStation2.jpg)
