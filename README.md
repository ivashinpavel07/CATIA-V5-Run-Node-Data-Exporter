# 🧰 CATIA V5 Run Node Data Exporter

🇷🇺 [Русский](#-русский) | 🇬🇧 [English](#-english)

CATIA V5 CATScript for exporting node coordinates and bend radii from selected piping and HVAC Runs to Microsoft Excel.

---

# 🇷🇺 Русский

## 📌 О проекте

**CATIA V5 Run Node Data Exporter** — CATScript для выгрузки данных узлов выбранных трасс (**Runs**) из CATIA V5 в Microsoft Excel.

Скрипт позволяет получить для каждого узла трассы:

* 🔢 номер узла;
* 📍 координату X;
* 📍 координату Y;
* 📍 координату Z;
* ↪️ радиус изгиба (**Bend Radius**).

Результат формируется в виде структурированной таблицы Excel.

Такой отчет может использоваться для:

* анализа геометрии существующих трасс;
* проверки координат узлов;
* подготовки технических данных;
* переноса информации о трассе;
* подготовки данных для последующего восстановления или повторного построения Run.

## 🔄 Основная идея

Логика работы выглядит следующим образом:

```text
CATIA V5 Run
      ↓
Arrangement Nodes
      ↓
CATScript
      ↓
Microsoft Excel
      ↓
Node coordinates + Bend Radius
```

Формат получаемых данных близок к формату, который используется стандартной функцией CATIA V5 **Routing with Defined Nodes**.

Эта штатная команда позволяет построить Run по заранее заданным координатам узлов.

Таким образом, скрипт решает обратную задачу:

**существующий Run → данные его узлов → Excel**

а полученные данные могут служить основой для последующей подготовки входного файла и повторного построения трассы средствами CATIA V5.

## 📊 Данные в Excel

Скрипт формирует следующие столбцы:

```text
NodeNum | X-coord | Y-coord | Z-coord | Bend radius
```

Для каждого нового выбранного Run нумерация узлов начинается снова с `1`.

Это соответствует логике стандартного механизма **Routing with Defined Nodes**, где появление нового узла с номером `1` означает начало следующего Run.

## 📘 CATIA V5 — Routing with Defined Nodes

Официальное описание Dassault Systèmes:

🔗 https://dshelp-embed.3ds.com/2025/english/CATIA_P3/online/ucfug_C2/ucfugbt1412.htm

В дистрибутиве CATIA V5 также имеется штатный пример входных данных:

```text
RunInputNodeData.txt
```

Например, для CATIA V5 B23:

```text
C:\Program Files\Dassault Systemes\B23\win_b64\startup\EquipmentAndSystems\MultiDiscipline\SampleData\RunInputNodeData.txt
```

Именно этот формат послужил ориентиром при формировании структуры Excel-отчета.

## 🎥 Видео

Демонстрация работы скрипта:

**🧰 CATIA V5 скрипт — отчет в Excel по траекториям трубопроводов и воздуховодов (Runs)**

▶️ https://youtu.be/ZvCca-am78g

---

# 🇬🇧 English

## 📌 About

**CATIA V5 Run Node Data Exporter** is a CATScript for exporting node data from selected CATIA V5 piping and HVAC **Runs** to Microsoft Excel.

For each Run node, the script exports:

* 🔢 Node number;
* 📍 X coordinate;
* 📍 Y coordinate;
* 📍 Z coordinate;
* ↪️ Bend Radius.

The result is generated as a structured Microsoft Excel worksheet.

The exported data can be useful for:

* analyzing existing route geometry;
* checking node coordinates;
* preparing engineering data;
* transferring route information;
* preparing data for reconstruction or re-routing of existing Runs.

## 🔄 Concept

The basic workflow is:

```text
CATIA V5 Run
      ↓
Arrangement Nodes
      ↓
CATScript
      ↓
Microsoft Excel
      ↓
Node coordinates + Bend Radius
```

The exported data structure is close to the format used by the standard CATIA V5 **Routing with Defined Nodes** functionality.

CATIA V5 can create a Run using predefined node coordinates.

This script performs the opposite operation:

**existing Run → node data → Excel**

The resulting data can therefore be used as a basis for preparing an input file for rebuilding a route using the standard CATIA V5 routing functionality.

## 📊 Excel output

The script creates the following columns:

```text
NodeNum | X-coord | Y-coord | Z-coord | Bend radius
```

Node numbering starts again from `1` for every selected Run.

This follows the logic used by **Routing with Defined Nodes**, where a new `NodeNum = 1` starts a new Run.

## 📘 CATIA V5 — Routing with Defined Nodes

Official Dassault Systèmes documentation:

🔗 https://dshelp-embed.3ds.com/2025/english/CATIA_P3/online/ucfug_C2/ucfugbt1412.htm

CATIA V5 also includes a sample node input file:

```text
RunInputNodeData.txt
```

For example, in CATIA V5 B23:

```text
C:\Program Files\Dassault Systemes\B23\win_b64\startup\EquipmentAndSystems\MultiDiscipline\SampleData\RunInputNodeData.txt
```

This standard CATIA sample was used as a reference for the Excel report structure.

## 🎥 Video demonstration

**🧰 CATIA V5 script — Excel report for piping and HVAC Run node data**

▶️ https://youtu.be/ZvCca-am78g

---

## 📄 License

This project is distributed under the **MIT License**.
