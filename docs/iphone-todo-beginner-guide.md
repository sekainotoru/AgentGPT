# iPhone 向け ToDo アプリ入門（初心者向け）

このガイドでは、**Swift + SwiftUI** を使って、
「追加」「完了チェック」「削除」ができるシンプルな ToDo アプリを作ります。

---

## 0. まず結論（なぜこの構成？）

初心者の方には次の理由で SwiftUI がおすすめです。

- iPhone アプリの公式な開発言語（Swift）なので情報が多い
- 画面を比較的短いコードで作れる
- Xcode だけで始められる（無料）

---

## 1. 開発準備

1. Mac に **Xcode** をインストール（App Store）
2. Xcode を開いて `Create a new Xcode Project`
3. テンプレートは `App` を選択
4. Product Name を `TodoApp` などに設定
5. Interface は `SwiftUI`、Language は `Swift`

> ここまでできたら「iPhone シミュレータで起動」できれば OK です。

---

## 2. 最小のデータ構造を作る

ToDo1 件を表すモデルを作ります。

`TodoItem.swift`（新規ファイル）

```swift
import Foundation

struct TodoItem: Identifiable {
    let id = UUID()
    var title: String
    var isDone: Bool = false
}
```

### ポイント

- `Identifiable` は一覧表示（List）で必要
- `id` は各 ToDo を区別するための番号
- `isDone` は完了・未完了の状態

---

## 3. 画面と状態を作る

`ContentView.swift` を次のようにします。

```swift
import SwiftUI

struct ContentView: View {
    @State private var todos: [TodoItem] = []
    @State private var newTodoTitle: String = ""

    var body: some View {
        NavigationStack {
            VStack(spacing: 12) {
                HStack {
                    TextField("やることを入力", text: $newTodoTitle)
                        .textFieldStyle(.roundedBorder)

                    Button("追加") {
                        addTodo()
                    }
                    .buttonStyle(.borderedProminent)
                }
                .padding(.horizontal)

                List {
                    ForEach($todos) { $todo in
                        HStack {
                            Button {
                                todo.isDone.toggle()
                            } label: {
                                Image(systemName: todo.isDone ? "checkmark.circle.fill" : "circle")
                                    .foregroundStyle(todo.isDone ? .green : .gray)
                            }

                            Text(todo.title)
                                .strikethrough(todo.isDone)
                                .foregroundStyle(todo.isDone ? .secondary : .primary)
                        }
                    }
                    .onDelete(perform: deleteTodo)
                }
            }
            .navigationTitle("ToDo")
        }
    }

    private func addTodo() {
        let trimmed = newTodoTitle.trimmingCharacters(in: .whitespacesAndNewlines)
        guard !trimmed.isEmpty else { return }

        todos.append(TodoItem(title: trimmed))
        newTodoTitle = ""
    }

    private func deleteTodo(at offsets: IndexSet) {
        todos.remove(atOffsets: offsets)
    }
}

#Preview {
    ContentView()
}
```

### ポイント

- `@State` は「画面で変化する値」を持つ
- `TextField` と `Button` で入力 → 追加
- `List + ForEach` で ToDo を表示
- `.onDelete` でスワイプ削除
- `strikethrough` で完了タスクを視覚化

---

## 4. 実行と動作確認

最低限、次の 3 つが動けば成功です。

1. テキストを入力して「追加」するとリストに増える
2. 丸アイコンをタップすると完了状態が切り替わる
3. セルを左スワイプして削除できる

---

## 5. 1 段階レベルアップ（保存機能）

次の目標は「アプリを閉じても ToDo が消えないこと」です。
初心者向けには以下どちらかがやりやすいです。

- **UserDefaults**: 小規模データなら簡単
- **SwiftData**: iOS 標準の新しいデータ保存

最初は UserDefaults、慣れたら SwiftData がおすすめです。

---

## 6. 学習の進め方（挫折しにくい順）

1. まずはこの最小版を写経して動かす
2. 次に「締切日」「優先度」を 1 つずつ追加
3. その後に保存機能を追加
4. 最後に UI を整える（色・並べ替え・フィルター）

> 一気に全部やろうとせず、**1 機能ずつ追加**が最短です。

---

## 7. よくある詰まりポイント

- Xcode のエラーが多くて読めない
  - まず最初の 1 件だけ読む
  - 直したら再ビルド
- `@State` の理解が曖昧
  - 「変わる値を UI に持たせるもの」と覚える
- 画面が更新されない
  - 値を `@State` / `@Binding` で扱えているか確認

---

## 8. 次に必要なら手伝えること

- この最小版に **期限日** を追加
- **カテゴリ分け**（仕事 / 私用）
- **並び替え**（未完了を上に）
- **SwiftData で永続化**

必要なら、次は「保存機能つき ToDo アプリ」を同じくステップ形式で一緒に作れます。
