### COUNT

**`COUNT`** 関数はSQLで非常によく使用される集約関数の一つで、特定の条件を満たす行の数をカウントするために使用されます。以下では、初級から応用までの **`COUNT`** 関数の使い方について説明します。

### **初級レベル**

**全行のカウント**：
テーブル内の全行の数をカウントします。

```sql
sqlCopy code
SELECT COUNT(*) FROM table_name;

```

**`COUNT(*)`**はテーブル内の全行の数を返します。

**特定の列の非NULL値のカウント**：
特定の列に含まれる非NULL値の数をカウントします。

```sql
sqlCopy code
SELECT COUNT(column_name) FROM table_name;

```

**`COUNT(column_name)`**は指定された列の非NULL値の数を返します。

### **中級レベル**

**条件を満たす行のカウント**：
**`WHERE`**句を使って特定の条件を満たす行の数をカウントします。

```sql
sqlCopy code
SELECT COUNT(*) FROM table_name WHERE condition;

```

このクエリは、指定された条件を満たす行の数を返します。

**DISTINCTキーワードの使用**：
ユニークな値の数をカウントします。

```sql
sqlCopy code
SELECT COUNT(DISTINCT column_name) FROM table_name;

```

**`COUNT(DISTINCT column_name)`**は、指定された列のユニークな値の数を返します。

### **応用レベル**

**グループ化されたデータのカウント**：
**`GROUP BY`**句を使用して、特定の列の値に基づいてグループ化されたデータの行数をカウントします。

```sql
sqlCopy code
SELECT column_name, COUNT(*) FROM table_name GROUP BY column_name;

```

このクエリは、指定された列に基づいてグループ化された各グループの行数を返します。

**条件付き集約のカウント（CASE文を使用）**：
特定の条件を満たす行のカウントを条件付きで行います。

```sql
sqlCopy code
SELECT COUNT(CASE WHEN condition THEN 1 ELSE NULL END) FROM table_name;

```

このクエリは、指定された条件を満たす行の数を返します。**`CASE`**文を使用することで、特定の条件に基づくカウントを柔軟に行うことができます。

**`COUNT`** 関数を使うことで、データの概要を把握したり、特定の条件にマッチするデータの量を調べたりする際に非常に便利です。上記の例を参考に、様々なシナリオで **`COUNT`** 関数を活用してみてください。