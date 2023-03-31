# QTreeWidget双击编辑节点

```c++
class ElemTree : public QTreeWidget {
    Q_OBJECT

public:
    BattleElemTree(QWidget *parent);
    ~BattleElemTree();
private slots:
    void slotDoubleClickItem(QTreeWidgetItem *item, int col);
    void slotFinishEdit();//双击编辑完成
};
```



```c++
#include "ElemTree.h"
#include <QDragEnterEvent>
#include <QLineEdit>

ElemTree::ElemTree(QWidget *parent)
: QTreeWidget(parent) {
    geoItem = nullptr;
    connect(this, SIGNAL(itemDoubleClicked(QTreeWidgetItem *, int)), this, SLOT(slotDoubleClickItem(QTreeWidgetItem *, int)));
}

ElemTree::~ElemTree() {
    
}

void ElemTree::slotDoubleClickItem(QTreeWidgetItem *item, int col) {
    doubleClickItem = nullptr;
    if (col == 0) {
        doubleClickItem = item;
        QLineEdit* lineEdit = new QLineEdit(this);
        lineEdit->setText(QStringLiteral("新建要素"));
        connect(lineEdit, SIGNAL(editingFinished()), this, SLOT(slotFinishEdit()));
        this->setItemWidget(doubleClickItem, 0, lineEdit);
    }
}

//槽函数 - 编辑完成
void ElemTree::slotFinishEdit() {
    if (doubleClickItem != nullptr) {
        QLineEdit *edit = qobject_cast<QLineEdit*>(this->itemWidget(doubleClickItem, 0));
        if (!edit) {
            return;
        }
        QString text = edit->text();
        this->removeItemWidget(doubleClickItem, 0);
        doubleClickItem->setText(0, text);
    }
}
```


