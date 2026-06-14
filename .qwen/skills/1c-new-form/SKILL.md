---
name: 1c-new-form
description: How to create a new managed form in a 1С data processor (Veritas project, ТестированиеЗнаний)
source: auto-skill
extracted_at: '2026-06-07T09:39:39.383Z'
---

# Создание новой управляемой формы в обработке 1С (Veritas)

Целевой проект: `D:\1C\VeritasGit\Veritas\DataProcessors\ТестированиеЗнаний`

## Контекст

Платформа: 1С 8.3.26+, управляемые формы, BSL.  
Ограничения (из AI_RULES.md): запрещены пустые строки внутри процедур/функций, обязательно клиент/серверное разделение.

## Структура файлов формы

Для новой формы с именем `ФормаИмя` нужно создать 3 файла + обновить родительский XML:

```
DataProcessors\ТестированиеЗнаний\Forms\
├── ФормаИмя.xml                          # (1) Метаданные формы
└── ФормаИмя\
    └── Ext\
        ├── Form.xml                      # (2) Описание элемента формы (дизайн)
        └── Form\
            └── Module.bsl                # (3) Модуль формы
```

Затем добавить в `DataProcessors\ТестированиеЗнаний.xml`:
```xml
<Form>ФормаИмя</Form>
```
в секцию `<ChildObjects>`.

## (1) Метаданные формы: `Forms\ФормаИмя.xml`

Шаблон:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<MetaDataObject xmlns="http://v8.1c.ru/8.3/MDClasses" ... version="2.19">
    <Form uuid="сгенерировать-новый-uuid">
        <Properties>
            <Name>ФормаИмя</Name>
            <Synonym>
                <v8:item>
                    <v8:lang>ru</v8:lang>
                    <v8:content>Отображаемое имя</v8:content>
                </v8:item>
            </Synonym>
            <Comment/>
            <FormType>Managed</FormType>
            <IncludeHelpInContents>false</IncludeHelpInContents>
            <UsePurposes>
                <v8:Value xsi:type="app:ApplicationUsePurpose">PlatformApplication</v8:Value>
                <v8:Value xsi:type="app:ApplicationUsePurpose">MobilePlatformApplication</v8:Value>
            </UsePurposes>
            <ExtendedPresentation/>
        </Properties>
    </Form>
</MetaDataObject>
```

**Ключевые моменты:**
- `uuid` — сгенерировать **новый** GUID. Не копировать UUID из другой формы.
- `UsePurposes` включает `PlatformApplication` и `MobilePlatformApplication` (как во всех формах проекта).
- `Name` (системное имя) и `Synonym` (отображаемое) — разные.

## (2) Описание формы: `Forms\ФормаИмя\Ext\Form.xml`

Шаблон:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Form xmlns="http://v8.1c.ru/8.3/xcf/logform" ... version="2.19">
    <AutoCommandBar name="ФормаКоманднаяПанель" id="-1"/>
    <ChildItems>
        <!-- Элементы формы: LabelDecoration, PictureDecoration, Button, LabelField и т.д. -->
    </ChildItems>
    <Attributes>
        <Attribute name="Объект" id="1">
            <Type>
                <v8:Type>cfg:DataProcessorObject.ТестированиеЗнаний</v8:Type>
            </Type>
            <MainAttribute>true</MainAttribute>
        </Attribute>
        <!-- Дополнительные реквизиты при необходимости -->
    </Attributes>
    <Commands>
        <!-- Команды (кнопки и их действия) -->
    </Commands>
    <Parameters>
        <!-- Параметры формы, передаваемые при открытии -->
    </Parameters>
</Form>
```

### Часто используемые элементы

**LabelDecoration (надпись-декорация):**
```xml
<LabelDecoration name="ДекорацияЗаголовок" id="N">
    <HorizontalStretch>true</HorizontalStretch>
    <Font ref="style:NormalTextFont" bold="true" italic="false" underline="false" strikeout="false" kind="StyleItem" scale="100"/>
    <Title formatted="false">
        <v8:item>
            <v8:lang>ru</v8:lang>
            <v8:content>Текст заголовка</v8:content>
        </v8:item>
    </Title>
    <HorizontalAlign>Center</HorizontalAlign>
    <ContextMenu name="ДекорацияЗаголовокКонтекстноеМеню" id="M"/>
    <ExtendedTooltip name="ДекорацияЗаголовокРасширеннаяПодсказка" id="K"/>
</LabelDecoration>
```

**PictureDecoration (картинка):**
```xml
<PictureDecoration name="ДекорацияКартинка" id="N">
    <Title formatted="false">
        <v8:item>
            <v8:lang>ru</v8:lang>
            <v8:content>Декорация картинка</v8:content>
        </v8:item>
    </Title>
    <GroupHorizontalAlign>Center</GroupHorizontalAlign>
    <Picture>
        <xr:Ref>CommonPicture.ИмяКартинки</xr:Ref>
        <xr:LoadTransparent>true</xr:LoadTransparent>
    </Picture>
    <ContextMenu name="ДекорацияКартинкаКонтекстноеМеню" id="M"/>
    <ExtendedTooltip name="ДекорацияКартинкаРасширеннаяПодсказка" id="K"/>
</PictureDecoration>
```

**Button (кнопка):**
```xml
<Button name="ЗакрытьФорму" id="N">
    <Type>UsualButton</Type>
    <HorizontalStretch>true</HorizontalStretch>
    <GroupHorizontalAlign>Center</GroupHorizontalAlign>
    <CommandName>Form.Command.ЗакрытьФорму</CommandName>
    <Title>
        <v8:item>
            <v8:lang>ru</v8:lang>
            <v8:content>Закрыть</v8:content>
        </v8:item>
    </Title>
    <ExtendedTooltip name="ЗакрытьФормуРасширеннаяПодсказка" id="K"/>
</Button>
```

**LabelField (поле-надпись, связанное с реквизитом):**
```xml
<LabelField name="ИмяРеквизита" id="N">
    <DataPath>ИмяРеквизита</DataPath>
    <Title>
        <v8:item>
            <v8:lang>ru</v8:lang>
            <v8:content>Отображаемый заголовок</v8:content>
        </v8:item>
    </Title>
    <ContextMenu name="ИмяРеквизитаКонтекстноеМеню" id="M"/>
    <ExtendedTooltip name="ИмяРеквизитаРасширеннаяПодсказка" id="K"/>
</LabelField>
```

### Реквизиты (Attributes)

Добавляются в секцию `<Attributes>`:

```xml
<Attribute name="ИмяРеквизита" id="N">
    <Title>
        <v8:item>
            <v8:lang>ru</v8:lang>
            <v8:content>Отображаемый заголовок</v8:content>
        </v8:item>
    </Title>
    <Type>
        <v8:Type>xs:decimal</v8:Type>
        <v8:NumberQualifiers>
            <v8:Digits>10</v8:Digits>
            <v8:FractionDigits>0</v8:FractionDigits>
            <v8:AllowedSign>Any</v8:AllowedSign>
        </v8:NumberQualifiers>
    </Type>
</Attribute>
```

Распространённые типы: `xs:decimal` (число), `xs:string` (строка), `cfg:CatalogRef.ИмяСправочника` (ссылка).

### Команды (Commands)

```xml
<Command name="ИмяКоманды" id="1">
    <Title>
        <v8:item>
            <v8:lang>ru</v8:lang>
            <v8:content>Отображаемое название команды</v8:content>
        </v8:item>
    </Title>
    <ToolTip>
        <v8:item>
            <v8:lang>ru</v8:lang>
            <v8:content>Подсказка</v8:content>
        </v8:item>
    </ToolTip>
    <Action>ИмяПроцедуры</Action>
</Command>
```

### Параметры (Parameters)

Параметры, передаваемые при открытии формы через `Параметры.ИмяПараметра`:

```xml
<Parameters>
    <Parameter name="ИмяПараметра">
        <Type>
            <v8:Type>xs:decimal</v8:Type>
            <v8:NumberQualifiers>
                <v8:Digits>3</v8:Digits>
                <v8:FractionDigits>0</v8:FractionDigits>
                <v8:AllowedSign>Any</v8:AllowedSign>
            </v8:NumberQualifiers>
        </Type>
    </Parameter>
</Parameters>
```

## (3) Модуль формы: `Forms\ФормаИмя\Ext\Form\Module.bsl`

```bsl
&НаКлиенте
Процедура ЗакрытьФорму(Команда)
	Закрыть();
КонецПроцедуры

&НаСервере
Процедура ПриСозданииНаСервере(Отказ, СтандартнаяОбработка)
	// Инициализация: чтение параметров, заполнение данных
	// ИмяРеквизита = Параметры.ИмяПараметра;
КонецПроцедуры
```

**Правила:**
- Пустых строк между строками кода внутри процедур/функций быть **не должно** (AI_RULES.md).
- Клиентские процедуры помечаются `&НаКлиенте`, серверные — `&НаСервере`.
- `ПриСозданииНаСервере` — основная серверная точка входа, где читаются `Параметры`.
- Если форма не принимает параметров и не требует инициализации, `ПриСозданииНаСервере` можно **полностью опустить** — не писать ни процедуру в модуле, **ни секцию `<Events>` в Form.xml**. Иначе будет объявлен обработчик, которого нет в модуле — код станет неаккуратным.

## (4) Регистрация в родительском объекте: `ТестированиеЗнаний.xml`

Найти секцию `<ChildObjects>` и добавить строку:

```xml
<Form>ФормаИмя</Form>
```

Порядок следования — обычно по алфавиту, но можно и в конец списка.

Убедиться, что имя формы в этой строке **точно совпадает** с `<Name>` из метаданных (регистр важен).

## Проверка перед завершением

- Все 3 файла созданы? `ФормаИмя.xml` + `Ext\Form.xml` + `Ext\Form\Module.bsl`
- UUID в метаданных — уникальный, не скопирован из другой формы.
- `FormType` = `Managed` (не `Ordinary`).
- Дочерний элемент зарегистрирован в `ТестированиеЗнаний.xml`.
- Нет пустых строк внутри процедур/функций в `.bsl`.
- Имена элементов (name/id) не конфликтуют с другими элементами формы.

## Открытие формы из кода

```bsl
ОткрытьФорму("DataProcessor.ТестированиеЗнаний.Form.ФормаИмя", Параметры, ЭтаФорма);
```

Где `Параметры` — структура с ключами, соответствующими `<Parameters>` в описании формы.

## Интеграция формы в игровой процесс (паттерн «флаг + клиентская процедура»)

Когда новая форма должна открываться при наступлении игрового события (например, поражение игрока), а код проверки события разбросан по серверным и клиентским процедурам, используйте паттерн:

### 1. Добавьте реквизит-флаг (Булево)

В `Ext\Form.xml` основной формы, в секцию `<Attributes>`:

```xml
<Attribute name="ИмяСобытия" id="следующий-id">
    <Title>
        <v8:item>
            <v8:lang>ru</v8:lang>
            <v8:content>Описание события</v8:content>
        </v8:item>
    </Title>
    <Type>
        <v8:Type>xs:boolean</v8:Type>
    </Type>
</Attribute>
```

### 2. В серверной/общей процедуре — устанавливайте флаг вместо прямого вызова UI

В процедуре, которая определяет наступление события (например, `ПроверитьПоражениеИгрока()`):

```bsl
// Вместо Сообщить("...") и ЭтаФорма.Доступность = Ложь:
ИмяСобытия = Истина;
ЭтаФорма.Доступность = Ложь;
```

### 3. Создайте клиентскую процедуру-открывашку

```bsl
&НаКлиенте
Процедура ПоказатьФормуСобытия()
    Если РежимВикторины = ПредопределенноеЗначение("Перечисление.РежимыВикторины.Восхождение") Тогда
        ОткрытьФорму("Обработка.ТестированиеЗнаний.Форма.ФормаИмя", ,
            ЭтаФорма, , , , , РежимОткрытияОкнаФормы.БлокироватьОкноВладельца);
    КонецЕсли;
КонецПроцедуры
```

### 4. Добавьте вызов открывашки во всех клиентских точках входа

**Точка 1 — когда проверка выполняется в клиентской процедуре напрямую:**

```bsl
Если ИгрокПобежден Тогда
    ПроверитьПоражениеИгрока();
    Если ЭтаФорма.Доступность = Ложь Тогда
        ПоказатьФормуСобытия();   // <-- добавить перед Возврат
        Возврат;
    КонецЕсли;
```

**Точка 2 — когда событие наступает после серверного вызова (например, `ДействияПротивника()`):**

```bsl
СтруктураДействия = ДействияПротивника();
// Проверить флаг сразу после возврата с сервера
Если ЭтаФорма.Доступность = Ложь И ИмяСобытия Тогда
    ПоказатьФормуСобытия();
    Возврат;
КонецЕсли;
// Далее — обычная обработка, если игрок не побеждён
ВариантДействия = СтруктураДействия.ВариантДействия;
```

### Почему это работает

- `ОткрытьФорму()` — клиентская функция, её нельзя вызвать напрямую из `&НаСервере`.
- Реквизиты формы синхронизируются между сервером и клиентом автоматически.
- Установка флага на сервере и `ЭтаФорма.Доступность = Ложь` — всё, что нужно; после возврата с сервера клиент проверяет флаг и открывает форму.
- `РежимОткрытияОкнаФормы.БлокироватьОкноВладельца` блокирует основную форму, пока модальная не закроется.
