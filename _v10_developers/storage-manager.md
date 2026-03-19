---
title: Storage Manager API
menu_title: Widget API - Storage Manager
tagline: Use the Storage Manager to save tabular data
class: developers
category: widgets api
---
# Materia Storage Manager

The score manager allows widgets to create and store tabular data.  Available for use in a widget instance player once you've loaded.

## Materia.Storage.Manager.addTable

Initialize a table to store data in. Call once per table.

| Object Property | Required | Description
|-
| tableName | **yes** | Name of the table to create. Avoid spaces and special characters, use underscores.
| columnName | **yes** | Name of the first column
| columnName | **no** | Add as many columns as you need
| ... | **no** | ...

### Example

```javascript
Materia.Storage.Manager.addTable('user_timing', 'click_target', 'time')
```

## Materia.Storage.Manager.getTable

Get a reference to the table for quick stuff.

| Object Property | Required | Description
|-
| tableName | **yes** | Name of the table to get. Should match the name it was created with.

### Example

```javascript
Materia.Storage.Manager.getTable('user_timing')
```

## Materia.Storage.Manager.insert

Add data into a table.

| Object Property | Required | Description
|-
| tableName | **yes** | Name of the table to add data to. Should match the name it was created with.
| firstColumnValue | **yes** | Name of the first column
| secondColumnValue | **no** | Add as many columns as you need
| ... | **no** | ...

### Example

```javascript
Materia.Storage.Manager.insert('user_timing', 'button_1', 20)
```

## Materia.Storage.Manager.clean

Convert a string into a storage table compatible name.

| Object Property | Required | Description
|-
| name | **yes** | String to convert

### Example

```javascript
Materia.Storage.Manager.clean('String to Clean')
```
