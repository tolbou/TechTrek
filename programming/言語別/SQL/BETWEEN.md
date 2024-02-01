### BETWEEN

**`BETWEEN`** 演算子はSQLで、指定した2つの値の範囲にある値を選択するために使用されます。この演算子は通常、数値、テキスト、日付データ型のカラムに対して使用され、指定した範囲に含まれる行のみをフィルタリングするのに便利です。**`BETWEEN`** 演算子の基本的な構文は以下の通りです：

```sql
sqlCopy code
SELECT column_names
FROM table_name
WHERE column_name BETWEEN value1 AND value2;

```

- **`column_names`**は、選択したいカラムの名前です。
- **`table_name`**は、クエリを実行するテーブルの名前です。
- **`column_name`**は、範囲検索を行うカラムの名前です。
- **`value1`**と**`value2`**は、検索範囲の開始値と終了値です。この範囲には**`value1`**と**`value2`**自身も含まれます。

### **数値に対する使用例**

従業員のテーブルがあり、その給与が特定の範囲にある従業員を見つけたい場合：

```sql
sqlCopy code
SELECT emp_no, salary
FROM salaries
WHERE salary BETWEEN 50000 AND 60000;

```

このクエリは、給与が50,000から60,000の範囲にあるすべての従業員の**`emp_no`**と**`salary`**を選択します。

### **日付に対する使用例**

特定の期間内に雇用された従業員を見つけたい場合：

```sql
sqlCopy code
SELECT emp_no, hire_date
FROM employees
WHERE hire_date BETWEEN '1990-01-01' AND '1990-12-31';

```

このクエリは、1990年1月1日から1990年12月31日の間に雇用された従業員の**`emp_no`**と**`hire_date`**を選択します。

### **テキストに対する使用例**

特定のアルファベット範囲の姓を持つ従業員を検索する場合：

```sql
sqlCopy code
SELECT emp_no, last_name
FROM employees
WHERE last_name BETWEEN 'Smith' AND 'Jones';

```

このクエリは、姓が**`Smith`**から**`Jones`**（これらを含む）の範囲にある従業員の**`emp_no`**と**`last_name`**を選択します。ただし、テキストの範囲を使用する場合、結果はデータベースのコラテーション（文字の比較方法）に依存することに注意してください。

### **注意点**

- **`BETWEEN`**演算子は両端の値を含む閉区間です。
- 範囲を逆にする（**`value2`** < **`value1`**）と、結果が空になる可能性があります。
- 時間や日付の範囲を指定する際には、時刻も含めるかどうかを考慮する必要があります。特に、終了日を含めたい場合は、終了日の**`23:59:59`**を指定することが一般的です。