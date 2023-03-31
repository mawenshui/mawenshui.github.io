# QStringList遍历，拿到其中的各个元素

```c++
    //读取文件内容
    QList<QList<QString>> listSurfaceDisplay;
    QStringList stringListRow;
    QList<QString> listRow;
    int i = 0;
    while(!l_SurfaceFile.atEnd())
    {
        //按行读取内容（回车符前的内容）
        QByteArray l_QByteArrayRowText = l_SurfaceFile.readLine();
        QString l_strRow(l_QByteArrayRowText);

        stringListRow = l_strRow.split(',');

        for(QList<QString>::iterator iter = stringListRow.begin(); iter != stringListRow.end(); iter++)
        {
            QString str = QString::number(iter->toInt());
            listRow.push_back(str);
        }

        listSurfaceDisplay.push_back(listRow);
        i++;
    }
```

