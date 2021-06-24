# Проект: П0268 Система управления S7-1200 для 536634 Simulation Factory 24v

Проект TIA Portal: VGR\_demo

Версия: rev2.0

### Назначение

Проект VGR\_demo предназначен для демонстрации работы манипулятора с вакуумным захватным устройством в составе макета fischertechnik 536634 Simulation Factory 24v. Исполняемая программа для ПЛК составлена на основе исходного кода проекта «fischertechnik Training Factory Industry 4.0 24V (en)» Для управления используется стойка управления СТ350 и контроллер SIMATIC S7-1214C. Версия TIA Portal – 16.

**Структура программы**

Организационные блоки:

- **Main [OB1]** – основной блок, который вызывает функциональные блоки движения осей манипулятора и основного алгоритма движения манипулятора. Параметр запуска на исполнение – фоновое;
- **Cyclic interrupt [OB30]** – служит для вызова функциональных блоков, отвечающих за обработку сигналов с энкодеров двигателей вертикальной, горизонтальной осей и вращения платформы. Параметр запуска на исполнение – с периодом T = 5 ms;
- **Startup [OB100]** – предназначен для перевода конечного автомата алгоритма в начальное состояние. Параметр запуска на исполнение – однократный запуск при старте системы.

Функциональные блоки:

- **FB\_Axis [FB22] –** управляет позиционированием одной оси манипулятора. Принимает на вход команды перемещения в исходное положение и в заданную позицию. Для каждой оси используется отдельный экземплярный блок данных. Блок вызывается периодически в функциональных блоках PRG\_VGR\_Axis\_horizontal [FB4], PRG\_VGR\_Axis\_rotate [FB5] и PRG\_VGR\_Axis\_vertical [FB1];
- **PRG\_VGR\_Axis\_vertical\_Act\_Pos [FB28]** – блок обработки сигналов от энкодера двигателя вертикальной оси. Читает текущее значение из регистра счетчика HSC и записывает его в соответствующее поле данных;
- **PRG\_VGR\_Axis\_horizontal\_Act\_Pos [FB29] –** блок обработки сигналов от энкодера двигателя горизонтальной оси;
- **PRG\_VGR\_Axis\_rotate\_Act\_Pos [FB30] -** блок обработки сигналов от энкодера двигателя привода платформы;
- **PRG\_VGR\_Axis\_horizontal [FB4] –** вызывает функциональный блок FB\_Axis [FB22], который формирует сигналы управления приводом горизонтальной оси;
- **PRG\_VGR\_Axis\_vertical [FB1] –** вызывает функциональный блок FB\_Axis [FB22], который формирует сигналы управления приводом вертикальной оси;
- **PRG\_VGR\_Axis\_rotate [FB5] –** вызывает функциональный блок FB\_Axis [FB22], который формирует сигналы управления приводом горизонтальной оси;

**PRG\_VGR\_Demo [FB3] –** главная программа манипулятора. Выполняет циклограмму перемещений манипулятора. Циклограмма реализована в виде конечного автомата. Блок вызывается в Main [OB1].

**Описание алгоритма PRG\_VGR\_Demo [FB3]**

1. Движение осей манипулятора в исходное положение (парковка в исходное положение);
1. Выдвинуть механизм загрузки заготовок в печь технологического участка;
1. Переместить вакуумный захват манипулятора к накопителю белых изделий;
1. Захватить изделие;
1. Переместить изделие на механизм загрузки в печь участка термической обработки;
1. Сбросить изделие;
1. Захватить изделие;
1. Переместить изделие в накопитель белых изделий;
1. Сбросить изделие;
1. Повтор цикла с п. 3.

### Конфигурация ПЛК

**Тип ПЛК**

В проекте используется ПЛК SIMATIC серии S7-1200 в следующем составе:

**Модули ПЛК**

|Название|Артикул|Версия ПО|Кол-во|
| :-: | :-: | :-: | :-: |
|CPU 1214C DC/DC/DC|6ES7 214-1AG40-0XB0|V4.4|1|
|SM 1223 DI16/DQ16 x 24VDC|6ES7 223-1BL32-0XB0|V2.0|2|

**Счетчики HSC**

- HSC\_3 – вертикальная ось;
- HSC\_4 – горизонтальная ось;
- HSC\_5 – вращение платформы.


**Адреса**

|Участок|Имя|Тип сигнала|Адрес|Комментарий|
| - | - | - | - | - |
|VGR|энкодер вертикальной оси A|DI-CNT|%I0.4|энкодер вертикальной оси A|
|VGR|энкодер вертикальной оси B|DI-CNT|%I0.5|энкодер вертикальной оси B|
|VGR|энкодер горизонтальной оси A|DI-CNT|%I0.6|энкодер горизонтальной оси A|
|VGR|энкодер горизонтальной оси B|DI-CNT|%I0.7|энкодер горизонтальной оси B|
|VGR|энкодер поворотной платформы A|DI-CNT|%I1.0|энкодер поворотной платформы A|
|VGR|энкодер поворотной платформы B|DI-CNT|%I1.1|энкодер поворотной платформы B|
|VGR|IX\_VGR\_RefSwitchVerticalAxis\_I1|DI|%I12.0|начальная позиция вертикальной оси|
|VGR|IX\_VGR\_RefSwitchHorizontalAxis\_I2|DI|%I12.1|начальная позиция горизонтальной оси|
|VGR|IX\_VGR\_RefSwitchRotate\_I3|DI|%I12.2|начальная позиция поворотной платформы|
|VGR|QX\_VGR\_M1\_VerticalAxisUp\_Q1|DO|%Q13.0|движение вертикальной оси вверх|
|VGR|QX\_VGR\_M1\_VerticalAxisDown\_Q2|DO|%Q13.1|движение вертикальной оси вниз|
|VGR|QX\_VGR\_M2\_HorizontalAxisBackward\_Q3|DO|%Q13.2|движение горизонтальной оси назад|
|VGR|QX\_VGR\_M2\_HorizontalAxisForward\_Q4|DO|%Q13.3|движение горизонтальной оси вперед|
|VGR|QX\_VGR\_M3\_RotateClockwise\_Q5|DO|%Q13.4|поворот платформы по часовой стрелке|
|VGR|QX\_VGR\_M3\_RotateCounterclockwise\_Q6|DO|%Q13.5|поворот платформы против часовой стрелки|
|VGR|QX\_VGR\_Compressor\_Q7|DO|%Q13.6|компрессор|
|VGR|QX\_VGR\_ValveVacuum\_Q8|DO|%Q13.7|вакуумный захват|
|MPO|IX\_MPO\_RefSwitchOvenFeederInside\_I6|DI|%I8.5|механизм загрузки в печь позиция (внутри)|
|MPO|IX\_MPO\_RefSwitchOvenFeederOutside\_I7|DI|%I8.6|механизм загрузки в печь позиция (снаружи)|
|MPO|QX\_MPO\_M4\_OvenFeederRetract\_Q5|DO|%Q8.2|задвинуть держатель заготовки|
|MPO|QX\_MPO\_M4\_OvenFeederExtend\_Q6|DO|%Q8.3|выдвинуть держатель заготовки|
|MPO|QX\_MPO\_Compressor\_Q10|DO|%Q8.7|компрессор|
|MPO|QX\_MPO\_ValveOvenDoor\_Q13|DO|%Q9.2|вакуумный насос (присоска)|

**Настройки**

Настройки CPU

- Фильтр каналов DI: 0.2 микросекунды;
- Функция счетчика HSC\_3: A/B counter fourfold;
- Функция счетчика HSC\_4: A/B counter fourfold;
- Функция счетчика HSC\_5: A/B counter fourfold.

### Электронные ресурсы

- SIEMENS Руководство по программированию S7-1200/S7-1500 81318674, V1.4,    11/2015;
- [Исходный код проекта fischertechnik Training Factory Industry 4.0 24V (en)](https://github.com/fischertechnik/plc_training_factory_24v/tree/master/PLC_SCL_sources).
