```c++
QMap<QString, QString>::iterator iter = m_map.begin();
while (iter != m_map.end())
{
    qDebug() << "Iterator " << iter.key() << ":" << iter.value(); // 迭代器
    iter++;
}
```

