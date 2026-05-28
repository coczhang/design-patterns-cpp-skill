# Qt Model/View Guide

Use Qt Model/View when data is naturally displayed as lists, tables, trees, logs, alarms, devices, scan results, configuration grids, or large data sets.

## When to Use

- Data appears in `QTableView`, `QTreeView`, `QListView`, or QML views.
- Data needs sorting/filtering through proxy models.
- Multiple views should display the same data.
- Data is large enough that item widgets become slow or hard to maintain.
- The UI needs delegates for custom display/editing.

Avoid custom models for tiny static forms where normal widgets are simpler.

## Core Types

| Type | Role |
| --- | --- |
| `QAbstractListModel` | one-dimensional list |
| `QAbstractTableModel` | row/column data |
| `QAbstractItemModel` | trees or custom hierarchy |
| `QSortFilterProxyModel` | sorting/filtering without changing source model |
| `QStyledItemDelegate` | custom rendering/editing |
| `QItemSelectionModel` | selection state shared across views |

## Design Rules

- Keep model data separate from widgets.
- Keep business operations out of `data()`; it should be cheap and predictable.
- Emit the correct model signals: `beginInsertRows`, `endInsertRows`, `dataChanged`, `layoutChanged`, `beginResetModel`, `endResetModel`.
- Use custom roles for QML or rich Qt Widgets display.
- Use proxy models for filtering/sorting instead of modifying source data for each view.
- Keep delegates focused on rendering/editing cells, not business workflows.

## Pattern Mapping

- Model acts like an Adapter from domain data to Qt views.
- Proxy model is a Qt-native Proxy/Decorator-like layer.
- Tree models often combine Composite concepts.
- Delegates can use Strategy-like formatting/editing policies.

## Minimal Table Model Sketch

```cpp
class AlarmTableModel final : public QAbstractTableModel {
    Q_OBJECT
public:
    explicit AlarmTableModel(QObject* parent = nullptr);

    int rowCount(const QModelIndex& parent = {}) const override;
    int columnCount(const QModelIndex& parent = {}) const override;
    QVariant data(const QModelIndex& index, int role) const override;
    QVariant headerData(int section, Qt::Orientation orientation, int role) const override;

    void setAlarms(QVector<AlarmRecord> alarms);

private:
    QVector<AlarmRecord> alarms_;
};
```

## Common Mistakes

- Using `QTableWidget` for large or frequently changing data.
- Calling network/database APIs from `data()`.
- Forgetting begin/end insert/remove signals.
- Resetting the model for every small change.
- Putting UI formatting logic inside domain objects.
- Mixing selection state with domain state.

## Testing

Test:

- row/column counts,
- role values,
- insertion/removal signals with `QSignalSpy`,
- proxy filtering/sorting,
- delegate-independent formatting rules.
