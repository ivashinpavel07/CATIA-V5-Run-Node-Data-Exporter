# 🧰 CATIA V5 Run Report Exporter

🇷🇺 [Русский](#-русский) | 🇬🇧 [English](#-english)

**CATIA V5 CATScript for exporting detailed piping and HVAC Run reports to Microsoft Excel.**

The report includes node coordinates, bend data, Run identification, route length, section parameters and calculated cross-sectional area.

🎥 [Video demonstration on YouTube](https://youtu.be/ZvCca-am78g)
📘 [CATIA Help — Routing with Defined Nodes](https://dshelp-embed.3ds.com/2025/english/CATIA_P3/online/ucfug_C2/ucfugbt1412.htm)

---

# 🇷🇺 Русский

## 📌 О проекте

**CATIA V5 Run Report Exporter** — CATScript для автоматического формирования подробного Excel-отчёта по выбранным трассам (**Runs**) в CATIA V5.

Скрипт предназначен прежде всего для работы с трассами:

* 🔧 трубопроводных систем;
* 🌬️ воздуховодов и вентиляционных систем;
* 📐 других объектов CATIA V5, построенных на базе `ArrangementRun`.

После выбора одного или нескольких Runs скрипт получает данные трасс и их узлов и автоматически создаёт новую книгу Microsoft Excel.

В отчёт попадают не только координаты узлов, но и геометрические и идентификационные параметры самой трассы.

---

## 📊 Что экспортируется

Для каждого узла выбранного Run формируются следующие столбцы:

|  № | Поле               | Описание                                                          |
| -: | ------------------ | ----------------------------------------------------------------- |
|  1 | `NodeNum`          | Номер узла внутри Run                                             |
|  2 | `X-coord`          | Координата X                                                      |
|  3 | `Y-coord`          | Координата Y                                                      |
|  4 | `Z-coord`          | Координата Z                                                      |
|  5 | `Bend radius`      | Радиус изгиба в узле                                              |
|  6 | `Bend angle`       | Угол изгиба в узле                                                |
|  7 | `Instance Name`    | Имя экземпляра Run                                                |
|  8 | `Part Number`      | Part Number выбранного Run                                        |
|  9 | `Run Length`       | Полная длина Run                                                  |
| 10 | `Section Type`     | Тип сечения: `Round` или `Rectangular`                            |
| 11 | `Section Diameter` | Диаметр круглого или эквивалентный диаметр прямоугольного сечения |
| 12 | `Section Height`   | Высота прямоугольного сечения                                     |
| 13 | `Section Width`    | Ширина прямоугольного сечения                                     |
| 14 | `Area, m^2`        | Расчётная площадь поперечного сечения, м²                         |

Для первой и последней точки Run в поле `Bend angle` записывается:

```text
Edge
```

поскольку эти точки являются краевыми узлами трассы.

На строку заголовков Excel автоматически устанавливается **AutoFilter**, поэтому полученный отчёт можно сразу сортировать и фильтровать.

---

## 🔄 Как работает скрипт

Общая логика:

```text
Selected Runs in CATIA V5
            ↓
     ArrangementRuns
            ↓
      ArrangementRun
            ↓
     ArrangementNodes
            ↓
   Coordinates / Bend data
            +
 Run / Section parameters
            ↓
     Microsoft Excel
            ↓
   Structured Run report
```

Для каждого выбранного Run скрипт получает коллекцию:

```text
ArrangementNodes
```

и последовательно обрабатывает каждый узел.

Координаты точки получаются через:

```text
ArrangementNode.GetPoint
```

а параметры изгиба — через:

```text
ArrangementNode.BendRadius
ArrangementNode.BendAngle
```

Дополнительно непосредственно из `ArrangementRun` считываются длина трассы и параметры её сечения.

---

## 📐 Круглые и прямоугольные сечения

Скрипт автоматически определяет тип сечения Run.

### 🔵 Round

Для круглого сечения в отчёт записываются:

* `Section Type = Round`;
* `Section Diameter`;
* площадь поперечного сечения.

Площадь рассчитывается по формуле:

```text
A = π × D² / 4
```

и записывается в столбец:

```text
Area, m^2
```

### ▭ Rectangular

Для прямоугольного сечения записываются:

* `Section Type = Rectangular`;
* `Section Height`;
* `Section Width`;
* эквивалентный диаметр;
* площадь сечения.

Площадь:

```text
A = Height × Width
```

Для столбца `Section Diameter` рассчитывается эквивалентный диаметр круглого сечения той же площади:

```text
Deq = √(4 × Height × Width / π)
```

В текущей реализации скрипта для π используется значение `3.14`.

---

## 💡 Зачем был создан скрипт

Первоначальная идея автоматизировать получение координат узлов существующих трасс CATIA V5 появилась благодаря предложению одного из моих коллег.

Задача была практической: вместо ручного определения координат узлов получить их сразу в структурированном виде в Excel.

По мере развития скрипта простой экспорт координат превратился в более полный отчёт по Run:

```text
Координаты узлов
       +
Параметры изгибов
       +
Длина трассы
       +
Тип и размеры сечения
       +
Площадь сечения
       +
Идентификация Run
```

В результате один Excel-файл можно использовать как для анализа геометрии трасс, так и для подготовки технических данных.

---

## 🔁 Связь с Routing with Defined Nodes

У экспорта координат есть ещё одно практическое применение.

В CATIA V5 существует штатная команда **Routing with Defined Nodes**, которая позволяет создавать Run по заранее определённым узловым точкам.

Для входных данных используются:

```text
NodeNum
X-coord
Y-coord
Z-coord
Bend radius
```

Первые пять столбцов отчёта данного скрипта специально имеют близкую структуру:

```text
NodeNum | X-coord | Y-coord | Z-coord | Bend radius
```

Поэтому скрипт можно рассматривать как инструмент для выполнения обратной задачи:

```text
Существующий Run
       ↓
Координаты и параметры его узлов
       ↓
Excel
       ↓
Подготовка входных данных
       ↓
Routing with Defined Nodes
       ↓
Новый Run
```

То есть данные существующей трассы можно быстро получить в Excel, при необходимости обработать и затем использовать как основу для подготовки файла для повторного построения трассы штатными средствами CATIA V5.

> ⚠️ **Важно:** Excel-файл, создаваемый скриптом, не является файлом прямого импорта для команды `Routing with Defined Nodes`. Перед импортом данные необходимо привести к формату входного файла, который ожидает CATIA V5.

---

## 📘 Штатный пример CATIA V5

Официальное описание Dassault Systèmes:

### Routing with Defined Nodes

🔗 https://dshelp-embed.3ds.com/2025/english/CATIA_P3/online/ucfug_C2/ucfugbt1412.htm

В стандартном дистрибутиве CATIA V5 имеется пример входного файла:

```text
RunInputNodeData.txt
```

Например, в CATIA V5 B23 он располагается в:

```text
C:\Program Files\Dassault Systemes\B23\win\_b64\startup\EquipmentAndSystems\MultiDiscipline\SampleData\RunInputNodeData.txt
```

Этот штатный пример является рабочим ориентиром для структуры данных узлов.

При чтении входного файла CATIA V5 рассматривает очередной:

```text
NodeNum = 1
```

как начало нового Run.

Поэтому при экспорте нескольких Runs данный скрипт также начинает нумерацию узлов каждого нового Run заново с `1`.

Например:

```text
Run 1:  1 → 2 → 3 → 4
Run 2:  1 → 2 → 3
Run 3:  1 → 2 → 3 → 4 → 5
```

---

## ⚙️ Как использовать

1. Откройте в **CATIA V5** сборку, содержащую необходимые Runs.

2. Выберите один или несколько Runs, по которым требуется сформировать отчёт.

3. Запустите скрипт:

   ```text
   RunReportNodeDataToExcelForSelectRuns.CATScript
   ```

   через стандартное окно запуска макросов CATIA V5.

4. Скрипт найдёт выбранные `ArrangementRun` и обработает их `ArrangementNodes`.

5. **Microsoft Excel** запустится автоматически.

6. В новой книге будет создан отчёт из 14 столбцов с данными узлов, трассы и её сечения.

7. Сохраните полученную книгу Excel вручную в нужном расположении и формате.

---

## 📋 Пример структуры отчёта

```text
NodeNum
X-coord
Y-coord
Z-coord
Bend radius
Bend angle
Instance Name
Part Number
Run Length
Section Type
Section Diameter
Section Height
Section Width
Area, m^2
```

При выборе нескольких Runs данные последовательно добавляются в одну таблицу.

При этом `Instance Name` и `Part Number` позволяют определить, к какой именно трассе относится каждая строка.

---

## ✅ Требования

Для работы необходимы:

* CATIA V5;
* сборка с объектами `ArrangementRun`;
* возможность запуска CATScript;
* Microsoft Excel;
* Windows с доступной COM-автоматизацией Excel.

---

## ⚠️ Ограничения текущей версии

* Обрабатываются только выделенные объекты, в имени которых присутствует строка `Run`.
* Выбранные Runs должны быть доступны через `ArrangementRuns` активного Product.
* При определённой структуре сборки может потребоваться открыть отдельное окно сборки, непосредственно содержащей выбранные Runs.
* Microsoft Excel должен быть установлен.
* Новая книга Excel создаётся автоматически, но **не сохраняется автоматически**.
* Значения координат, длины и размеров записываются в Excel как числовые значения, полученные из CATIA.
* Площадь поперечного сечения дополнительно рассчитывается скриптом и записывается в м².
* Для прямоугольного сечения `Section Diameter` является расчётным эквивалентным диаметром.
* Excel-отчёт не является готовым файлом прямого импорта для `Routing with Defined Nodes`.

---

## 🎥 Видео

Работа скрипта показана в видео:

### 🧰 CATIA V5 скрипт — отчёт в Excel по траекториям трубопроводов и воздуховодов (Runs)

▶️ https://youtu.be/ZvCca-am78g

В видео показан практический пример получения Excel-отчёта по существующим трассам CATIA V5.

---

## 📥 Скрипт

Основной файл проекта:

[`RunReportNodeDataToExcelForSelectRuns.CATScript`](./RunReportNodeDataToExcelForSelectRuns.CATScript)

---

## 📄 Лицензия

Проект распространяется по лицензии **MIT License**.

Подробнее:

[`LICENSE`](./LICENSE)

---

# 🇬🇧 English

## 📌 About

**CATIA V5 Run Report Exporter** is a CATScript for automatically generating a detailed Microsoft Excel report for selected CATIA V5 **Runs**.

The script is intended primarily for:

* 🔧 piping systems;
* 🌬️ HVAC and ducting systems;
* 📐 other CATIA V5 systems based on `ArrangementRun` objects.

After one or more Runs are selected, the script extracts both node data and Run parameters and automatically creates a new Microsoft Excel workbook.

The resulting report contains not only node coordinates but also bend information, Run identification, route length and cross-section parameters.

---

## 📊 Exported data

The report contains 14 columns:

|  # | Field              | Description                                      |
| -: | ------------------ | ------------------------------------------------ |
|  1 | `NodeNum`          | Node number within the Run                       |
|  2 | `X-coord`          | X coordinate                                     |
|  3 | `Y-coord`          | Y coordinate                                     |
|  4 | `Z-coord`          | Z coordinate                                     |
|  5 | `Bend radius`      | Node bend radius                                 |
|  6 | `Bend angle`       | Node bend angle                                  |
|  7 | `Instance Name`    | Run instance name                                |
|  8 | `Part Number`      | Run Part Number                                  |
|  9 | `Run Length`       | Total Run length                                 |
| 10 | `Section Type`     | `Round` or `Rectangular`                         |
| 11 | `Section Diameter` | Round diameter or calculated equivalent diameter |
| 12 | `Section Height`   | Rectangular section height                       |
| 13 | `Section Width`    | Rectangular section width                        |
| 14 | `Area, m^2`        | Calculated cross-sectional area                  |

For the first and last nodes of a Run, the `Bend angle` field is marked:

```text
Edge
```

The script also applies an **AutoFilter** to the Excel header row, making the generated report ready for sorting and filtering.

---

## 🔄 How it works

The basic workflow is:

```text
Selected Runs in CATIA V5
            ↓
     ArrangementRuns
            ↓
      ArrangementRun
            ↓
     ArrangementNodes
            ↓
   Coordinates / Bend data
            +
 Run / Section parameters
            ↓
     Microsoft Excel
            ↓
   Structured Run report
```

For each selected Run, the script retrieves:

```text
ArrangementNodes
```

and processes every node.

Node coordinates are obtained using:

```text
ArrangementNode.GetPoint
```

while bend information is obtained using:

```text
ArrangementNode.BendRadius
ArrangementNode.BendAngle
```

Run length and section parameters are retrieved directly from the `ArrangementRun` object.

---

## 📐 Round and rectangular sections

The script automatically determines the section type.

### 🔵 Round

For round sections, the report contains:

* `Section Type = Round`;
* `Section Diameter`;
* calculated cross-sectional area.

The area is calculated as:

```text
A = π × D² / 4
```

and stored as:

```text
Area, m^2
```

### ▭ Rectangular

For rectangular sections, the report contains:

* `Section Type = Rectangular`;
* `Section Height`;
* `Section Width`;
* calculated equivalent diameter;
* calculated cross-sectional area.

The area is:

```text
A = Height × Width
```

The value stored in `Section Diameter` is the equivalent circular diameter for the same cross-sectional area:

```text
Deq = √(4 × Height × Width / π)
```

The current script implementation uses `3.14` for π.

---

## 💡 Background

The original idea of automating the export of existing CATIA V5 Run node coordinates came from a suggestion by one of my colleagues.

The practical goal was simple: instead of manually obtaining each node coordinate, export the complete set directly to Excel.

As the script evolved, the original node-coordinate exporter became a more complete Run reporting tool combining:

```text
Node coordinates
       +
Bend parameters
       +
Run length
       +
Section type and dimensions
       +
Cross-sectional area
       +
Run identification
```

This makes the generated Excel workbook useful both for geometry analysis and for preparing engineering data.

---

## 🔁 Relationship with Routing with Defined Nodes

CATIA V5 provides the standard **Routing with Defined Nodes** functionality for creating a Run from predefined node points.

The standard input data uses:

```text
NodeNum
X-coord
Y-coord
Z-coord
Bend radius
```

The first five columns of this report intentionally have a similar structure:

```text
NodeNum | X-coord | Y-coord | Z-coord | Bend radius
```

This allows the script to also be used as part of a reverse workflow:

```text
Existing Run
      ↓
Node coordinates and parameters
      ↓
Excel
      ↓
Prepare input data
      ↓
Routing with Defined Nodes
      ↓
New Run
```

Data from an existing Run can therefore be exported, processed if necessary, and used as a basis for preparing an input file for rebuilding a route using standard CATIA V5 functionality.

> ⚠️ **Important:** the Excel workbook generated by this script is not a direct input file for `Routing with Defined Nodes`. The data must first be converted into the input format expected by CATIA V5.

---

## 📘 CATIA V5 sample data

Official Dassault Systèmes documentation:

### Routing with Defined Nodes

🔗 https://dshelp-embed.3ds.com/2025/english/CATIA_P3/online/ucfug_C2/ucfugbt1412.htm

CATIA V5 includes a standard sample input file:

```text
RunInputNodeData.txt
```

For example, in CATIA V5 B23:

```text
C:\Program Files\Dassault Systemes\B23\win\_b64\startup\EquipmentAndSystems\MultiDiscipline\SampleData\RunInputNodeData.txt
```

This standard sample provides a working reference for the node-data structure.

When CATIA V5 encounters another:

```text
NodeNum = 1
```

it starts a new Run.

For this reason, the script also restarts node numbering from `1` for every selected Run.

Example:

```text
Run 1:  1 → 2 → 3 → 4
Run 2:  1 → 2 → 3
Run 3:  1 → 2 → 3 → 4 → 5
```

---

## ⚙️ How to use

1. Open the CATIA V5 assembly containing the required Runs.

2. Select one or more Runs to include in the report.

3. Run:

   ```text
   RunReportNodeDataToExcelForSelectRuns.CATScript
   ```

   using the standard CATIA V5 macro execution dialog.

4. The script retrieves the selected `ArrangementRun` objects and their `ArrangementNodes`.

5. **Microsoft Excel** starts automatically.

6. A new workbook is created containing the 14-column Run report.

7. Save the resulting Excel workbook manually in the required location and format.

---

## 📋 Report structure

```text
NodeNum
X-coord
Y-coord
Z-coord
Bend radius
Bend angle
Instance Name
Part Number
Run Length
Section Type
Section Diameter
Section Height
Section Width
Area, m^2
```

When several Runs are selected, all data is added sequentially to the same worksheet.

`Instance Name` and `Part Number` make it possible to identify which Run each row belongs to.

---

## ✅ Requirements

* CATIA V5;
* CATIA V5 assembly containing `ArrangementRun` objects;
* CATScript execution enabled;
* Microsoft Excel;
* Windows environment with Microsoft Excel COM automation available.

---

## ⚠️ Current limitations

* Only selected objects whose names contain `Run` are processed.
* Selected Runs must be accessible through `ArrangementRuns` of the active Product.
* Depending on the assembly structure, it may be necessary to open a separate assembly window containing the selected Runs directly.
* Microsoft Excel must be installed.
* The workbook is created automatically but is **not saved automatically**.
* Coordinate, length and dimension values are written as numerical values returned by CATIA.
* Cross-sectional area is calculated by the script and stored in m².
* For rectangular Runs, `Section Diameter` is a calculated equivalent diameter.
* The generated Excel workbook is not a direct `Routing with Defined Nodes` input file.

---

## 🎥 Video demonstration

### 🧰 CATIA V5 script — Excel report for piping and HVAC Runs

▶️ https://youtu.be/ZvCca-am78g

The video demonstrates the script on actual CATIA V5 Run geometry and shows the resulting Excel report.

---

## 📥 Script

Main project file:

[`RunReportNodeDataToExcelForSelectRuns.CATScript`](./RunReportNodeDataToExcelForSelectRuns.CATScript)

---

## 📄 License

This project is distributed under the **MIT License**.

See:

[`LICENSE`](./LICENSE)

---

## ℹ️ Disclaimer

This is an independent utility and is not an official Dassault Systèmes product.

CATIA and Dassault Systèmes product names are trademarks or registered trademarks of their respective owners.
