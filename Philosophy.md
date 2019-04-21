## Link for the section

http://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#p-philosophy

## P.1

**用程式表達一切**

* 能讓開發者光看程式就懂在幹麻，而不用額外去讀很多註解
* 該宣告const就宣告const，可以避免意外的錯誤與提升可讀性
    * e.g. method宣告為const代表他不會去動到該object的state(i.e. properties)
* 適當的使用標準函式庫遠比自己寫實作好，節省時間、避免錯誤，也增加可讀性
* 必要時，適當的用物件取代scalar type作為function的參數，可以增加可讀性

## P.2

**使用 ISO Standard C++ 來寫程式**

使用標準C++來編寫核心程式，至於環境依賴的library、extension，做個介面去包裝他們，使他們可拆分、分開編譯，這樣一來可以提升核心程式的可攜性(portability)。

* 使用標準C++並不保證可攜性，一些未定義的行為，可能會根據不同環境的編譯器而有不同的結果
  ex:

    ```c++
    v[i] = ++i;
    ```

  到底 `++i` 會先運算呢？還是會先被拿來用在 `v[i]` ？ 避免這種未知行為的寫法會是比較好的選擇。

* 注意會因實作定義而改變的constructs， e.g. `sizeof(int)`

## P.3

**表達意圖**

除非一些程式碼的意圖有被敘述(e.g. **名稱**或**註解**)，不然無法判斷該程式碼有沒有正確做它該做的事

e.g.

```c++
gsl::index i = 0;
while (i < v.size()) {
    // ... do something with v[i] ...
}
```

這邊沒有表達出**只要**loop所有 `v` 的elements，而且
1. 暴露了index的實作細節
2. `i` 在loop外依舊存在，這有可能不是想要的，而讀者不會知道

比較好的:

```c++
for (const auto& x : v) { /* do something with the value of x */ }
```

這樣就不會暴露iteration的機制，而且 `x` 被設定為 `v` 的elements的const reference，所以可以知道這個動作不會去改變 `v` 的elements。
如果想要修改elements，可以寫成這樣：

```c++
for (auto& x : v) { /* modify x */ }
```

另外有時也可以使用 `for_each` 這種語意化的結構，它更直接地表達了意圖

e.g.

```c++
for_each(par, v, [](int x) { /* do something with the value of x */ });
```

* 寫code原則：表達**該做什麼**，而不是**該怎麼做**
* 有些結構可以更清楚的表達意圖

  e.g.

    ```c++
    // 用兩個點畫一條線
    draw_line(int, int, int, int); // 含糊
    draw_line(Point, Point); // 清楚
    ```
