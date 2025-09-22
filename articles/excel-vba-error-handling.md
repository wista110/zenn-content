---
title: "ExcelVBA On Error GoTo完全ガイド：エラー処理の基本から実践的なバッチ処理まで"
emoji: "📊"
type: "tech"
topics: ["excel", "vba", "エラー処理", "プログラミング", "初心者"]
published: true
---

## はじめに

ExcelVBAを学んでいると、必ずと言っていいほど遭遇するのが**エラー処理**です。特に`On Error GoTo`は、プログラムを堅牢にするために欠かせない構文ですが、正しい使い方を理解していないと、かえってバグの温床になってしまうこともあります。

この記事では、`On Error GoTo`の基本を実際のコード例とともに解説したいと思います。

※ちなみにほぼ自分用のメモみたいなもんです。良かったら参考にしてください。😉

## On Error GoTo とは

`On Error GoTo`は、VBAでエラーが発生した際に、指定したラベルへ処理をジャンプさせる構文です。これにより、予期しないエラーでもプログラムを止めずに対応できます。

### 簡単にエラー処理の説明

```vba
' エラー処理なしの例
Sub BadExample()
    Dim x As Integer
    x = 1 / 0  ' ゼロ除算エラー → プログラム停止
    MsgBox "この処理は実行されない"
End Sub
```
![メッセージが表示されない](/images/excel-vba-error-handling/baderror.png)
*図1: エラーの場合*
このコードは、ゼロ除算エラーでプログラムが停止してしまいます。
そのためMsgBoxの出力がされません。(図1)

エラー処理を追加することで、このような問題を回避できます。

## 基本構文と使い方

### 基本的なエラー処理

```vba
Sub BasicErrorHandling()
    On Error GoTo ErrorHandler

    ' 通常処理
    Dim x As Integer
    x = 1 / 0  ' ゼロ除算 → エラー発生

    Exit Sub

ErrorHandler:
    MsgBox "エラー発生: " & Err.Description
End Sub
```

![エラー処理の基本例](/images/excel-vba-error-handling/basicerror.png)
*図2: On Error GoToを使う場合*
エラーが起きてもMsgBoxが出力されます。(図2)

## よく使うエラー処理構文一覧

| 構文 | 説明 |
|------|------|
| `On Error GoTo ラベル名` | エラー発生時に指定ラベルへジャンプ |
| `On Error Resume Next` | エラーを無視して次の行へ進む |
| `On Error GoTo 0` | エラー処理を解除する |
| `Resume` | エラー発生行に戻って再実行 |
| `Resume Next` | エラー発生行の次から再開 |


## 本題：バッチ処理でのエラー集約

先ほどの例では使いどころがわからないですよね、。
実際の使い方としては、**エラーが起きても処理を続けていき、最後に何のエラーが起きたのかまとめて表示する**といった使い方が多いと思います。

### やりたいこと

- 複数の処理を順番に実行する
- 途中でエラーが起きても止まらず、最後まで実行する
- エラーが起きた処理を記録して、最後に一覧表示する

### 実装コード

```vba
Sub BatchProcessWithErrorLogging()
    Dim errorLog As String
    Dim i As Integer

    errorLog = ""

    For i = 1 To 5
        On Error GoTo ErrorHandler

        ' 処理例：0除算や存在しないファイルアクセスなど
        Select Case i
            Case 1
                MsgBox "処理 " & i & ": 正常"
            Case 2
                MsgBox "処理 " & i & ": " & 1 / 0  ' エラー発生
            Case 3
                MsgBox "処理 " & i & ": 正常"
            Case 4
                Open "C:\存在しないファイル.txt" For Input As #1  ' エラー発生
            Case 5
                MsgBox "処理 " & i & ": 正常"
        End Select

        On Error GoTo 0  ' エラー処理を解除
        GoTo ContinueLoop

ErrorHandler:
        errorLog = errorLog & "処理 " & i & " でエラー: " & Err.Description & vbCrLf
        Resume ContinueLoop

ContinueLoop:
    Next i

    ' 最後にエラー一覧を表示
    If errorLog <> "" Then
        MsgBox "以下の処理でエラーが発生しました:" & vbCrLf & errorLog
    Else
        MsgBox "すべての処理が正常に完了しました。"
    End If
End Sub
```

### コード解説

1. **`On Error GoTo ErrorHandler`**: 各処理でエラーが起きたらジャンプ
2. **`Resume ContinueLoop`**: 次のループへ進む
3. **`errorLog`**: エラー内容を蓄積
4. **`On Error GoTo 0`**: エラー処理を解除しておくことで、次の処理に影響しない

### 結果画像

![処理結果](/images/excel-vba-error-handling/image.png)
*図2: エラーがまとめて出力されている*

このように出力されると、共通の問題で複数のエラーが出ている場合などの原因の特定がしやすくなります。


## 実践的なエラー処理パターン

下記のコードはAI出力で基本の形を出してもらったものです。

### パターン1: エラーログを残す

```vba
Sub ErrorLogging()
    Dim errorLog As String
    errorLog = ""
    
    On Error GoTo ErrorHandler
    
    ' 処理1: ファイル操作
    Open "C:\存在しないファイル.txt" For Input As #1
    Close #1
    
    ' 処理2: 計算処理
    Dim result As Double
    result = 1 / 0
    
    Exit Sub

ErrorHandler:
    ' エラー情報をログに記録
    errorLog = "エラー番号: " & Err.Number & vbCrLf & _
               "エラー内容: " & Err.Description & vbCrLf & _
               "発生場所: " & Err.Source & vbCrLf & _
               "時刻: " & Now()
    
    ' ログファイルに書き込み
    Dim logFile As String
    logFile = "C:\temp\error_log.txt"
    Open logFile For Append As #2
    Print #2, errorLog & vbCrLf & "---" & vbCrLf
    Close #2
    
    MsgBox "エラーが発生しました。ログを確認してください。"
End Sub
```

### パターン2: エラー番号による条件分岐

```vba
Sub ConditionalErrorHandling()
    On Error GoTo ErrorHandler
    
    ' 様々な処理を実行
    Dim x As Integer
    x = 1 / 0  ' エラー発生
    
    Exit Sub

ErrorHandler:
    Select Case Err.Number
        Case 11  ' ゼロ除算エラー
            MsgBox "ゼロ除算エラーが発生しました。値を確認してください。"
        Case 53  ' ファイルが見つからない
            MsgBox "ファイルが見つかりません。パスを確認してください。"
        Case 1004  ' アプリケーションエラー
            MsgBox "Excelの操作でエラーが発生しました。"
        Case Else
            MsgBox "予期しないエラーが発生しました: " & Err.Description
    End Select
End Sub
```

## より実用的なバッチ処理例

### 複数ファイルの処理

```vba
Sub ProcessMultipleFiles()
    Dim fileList As Variant
    Dim errorLog As String
    Dim i As Integer
    
    ' 処理対象ファイルのリスト
    fileList = Array("file1.xlsx", "file2.xlsx", "file3.xlsx", "存在しないファイル.xlsx")
    errorLog = ""
    
    For i = 0 To UBound(fileList)
        On Error GoTo ErrorHandler
        
        ' ファイルを開く
        Workbooks.Open "C:\data\" & fileList(i)
        
        ' 何らかの処理（例：データの集計）
        Dim ws As Worksheet
        Set ws = ActiveSheet
        ws.Cells(1, 1).Value = "処理完了: " & Now()
        
        ' ファイルを保存して閉じる
        ActiveWorkbook.Save
        ActiveWorkbook.Close
        
        On Error GoTo 0
        GoTo ContinueLoop

ErrorHandler:
        errorLog = errorLog & "ファイル " & fileList(i) & " の処理でエラー: " & Err.Description & vbCrLf
        Resume ContinueLoop

ContinueLoop:
    Next i
    
    ' 結果表示
    If errorLog <> "" Then
        MsgBox "以下のファイルでエラーが発生しました:" & vbCrLf & errorLog
    Else
        MsgBox "すべてのファイルの処理が正常に完了しました。"
    End If
End Sub
```

## エラー処理のベストプラクティス

### ✅ 良い例

```vba
Sub GoodErrorHandling()
    Dim errorLog As String
    errorLog = ""
    
    On Error GoTo ErrorHandler
    
    ' 具体的な処理
    Dim result As Double
    result = CalculateSomething()
    
    ' 成功時の処理
    MsgBox "処理が完了しました。結果: " & result
    Exit Sub

ErrorHandler:
    ' エラー情報を詳細に記録
    errorLog = "エラー番号: " & Err.Number & vbCrLf & _
               "エラー内容: " & Err.Description & vbCrLf & _
               "発生時刻: " & Now()
    
    ' ユーザーに分かりやすいメッセージを表示
    MsgBox "処理中にエラーが発生しました。" & vbCrLf & _
           "詳細: " & Err.Description & vbCrLf & _
           "エラーログを確認してください。"
    
    ' ログファイルに記録
    WriteErrorLog errorLog
End Sub
```

### ❌ 悪い例

```vba
Sub BadErrorHandling()
    On Error Resume Next  ' エラーを無視
    
    ' エラーが発生しても気づかない
    Dim x As Integer
    x = 1 / 0
    
    ' 結果がおかしくても処理が続く
    MsgBox "結果: " & x  ' 0が表示される
    
    ' エラー処理を解除していない
    ' 後続の処理に影響する可能性
End Sub
```

## 重要なポイント

### 1. ログを残す
エラー内容をファイルに書き出すと、後で原因分析がしやすくなります。

### 2. 条件分岐
エラー番号によって処理を分けると、より堅牢なコードになります。

### 3. Resume Nextの乱用はNG
エラーを無視すると、バグの温床になります。

### 4. エラー処理の解除
`On Error GoTo 0`でエラー処理を解除しておくことで、意図しない動作を防げます。

## まとめ

`On Error GoTo`は、VBAで堅牢なプログラムを作るために欠かせない構文です。基本的な使い方から実践的なバッチ処理まで、適切にエラー処理を実装することで、ユーザーにとって使いやすく、開発者にとって保守しやすいコードを書くことができます。

**重要なポイント:**
- エラーは必ず記録する
- ユーザーに分かりやすいメッセージを表示する
- エラー処理の解除を忘れない
- `Resume Next`の乱用は避ける

エラー処理を適切に実装することで、より信頼性の高いExcelVBAアプリケーションを作成できます。

## 参考リンク

- [Microsoft公式: VBAエラー処理](https://docs.microsoft.com/ja-jp/office/vba/language/concepts/getting-started/error-handling)
- [VBAエラー番号一覧](https://docs.microsoft.com/ja-jp/office/vba/language/reference/user-interface-help/error-messages)
