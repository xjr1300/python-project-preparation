# pythonプロジェクトの準備方法と実装方法

- [pythonプロジェクトの準備方法と実装方法](#pythonプロジェクトの準備方法と実装方法)
  - [方針](#方針)
  - [`pyenv`と`poetry`のインストール](#pyenvとpoetryのインストール)
    - [プロジェクトディレクトリに仮想環境を作成するように設定](#プロジェクトディレクトリに仮想環境を作成するように設定)
  - [プロジェクトの準備方法](#プロジェクトの準備方法)
  - [ruff導入](#ruff導入)
    - [仮想環境にインストール](#仮想環境にインストール)
    - [拡張機能のインストール](#拡張機能のインストール)
  - [mypy導入](#mypy導入)
    - [仮想環境にインストール](#仮想環境にインストール-1)
    - [拡張機能のインストール](#拡張機能のインストール-1)
  - [pre-commit導入](#pre-commit導入)
  - [`vscode`のエディタ設定](#vscodeのエディタ設定)
    - [改行コードについて](#改行コードについて)
  - [その他`vscode`拡張機能の導入](#その他vscode拡張機能の導入)
  - [実装方針](#実装方針)
    - [`docstring`と型注釈](#docstringと型注釈)
    - [関数やメソッドの引数や戻り値の型](#関数やメソッドの引数や戻り値の型)
    - [型アノテーションについて](#型アノテーションについて)
    - [テスト](#テスト)
  - [実装例](#実装例)
    - [`my_package`プログラムを実装する例](#my_packageプログラムを実装する例)
    - [プロジェクトにモジュールと別プログラムを実装する例](#プロジェクトにモジュールと別プログラムを実装する例)

## 方針

- `poetry`でプロジェクト及びパッケージの依存関係を管理
- `ruff`でコードを検証及び整形
- `mypy`でタイプセーフなコードを記述することを目指す（あくまで目標）
- `pre-commit`でリポジトリにコミットする前にコードを整形

## `pyenv`と`poetry`のインストール

`pyenv`は、[ここ](https://github.com/pyenv/pyenv?tab=readme-ov-file#installation)を参照してインストールしてください。

`poetry`は、[ここ](https://python-poetry.org/docs/#installation)を参照してインストールしてください。

### プロジェクトディレクトリに仮想環境を作成するように設定

`poetry`はデフォルトで仮想環境を特定のディレクトリに作成するため、次の通りプロジェクトディレクトリに仮想環境を作成するように設定します。

```sh
poetry config virtualenvs.in-project true
```

## プロジェクトの準備方法

次の通り、ターミナルで`poetry`を実行してプロジェクトを作成します。
作成されたプロジェクトディレクトリをカレントディレクトリに変更後、プロジェクトディレクトリを`vscode`を開きます。

```sh
# `my-package`プロジェクトデを作成します。
poetry new my-package
# プロジェクトディレクトリをカレントディレクトリに変更します。
cd my-package
# `vscode`でプロジェクトディレクトリを開きます。
code .
```

`vscode`のターミナルを使用して、プロジェクトを次の通り設定します。

```sh
# プロジェクトで使用するpythonのバージョンを指定します。
# .python-versionファイルが作成され、次のコマンドで指定したバージョンがそのファイルに記録されます。
# ex. `pyenv local 3.12.0`
pyenv local <version>
# .gitignoreファイルをプロジェクトディレクトリに作成します（内容は後述）。
vi .gitignore
# gitリポジトリの初期化します。
git init
# 変更をステージングします。
git add --all
# ステージングした内容をリポジトリにコミットします。
git commit -m "Initial commit."
```

- `.gitignore`ファイルの内容

```text
# python
__pycache__/
*.py[cod]

# distribution / packaging
build/
dist/
sdist/

# unit test / coverage reports
.coverage
.coverage.*
.cache
coverage.xml

# translations
*.mo
*.pot

# sphinx documentation
docs/_build/

# dotenv
.env

# virtual environment
.venv/
venv/

# ruff
.ruff_cache/

# mypy
.mypy_cache/
```

- 現在のファイルシステム構成

  ```text
  my-package
  ├── .git              # gitリポジトリ
  ├── .gitignore        # git追跡対象外設定ファイル
  ├── .python-version   # pyenvのバージョン設定ファイル
  ├── .venv             # python仮想環境
  ├── README.md
  ├── my_package        # my_packageモジュールディレクトリ
  │   └── __init__.py
  ├── pyproject.toml    # poetry設定ファイル
  └── tests             # テストコードディレクトリ
      └── __init__.py
  ```

## ruff導入

### 仮想環境にインストール

次の通り仮想環境に`ruff`をインストールします。

```sh
poetry add ruff
```

`ruff`を使用するために、`pyproject.toml`ファイルに次の設定を追加します。

```toml
[tool.ruff]

# モジュールディレクトリとテストディレクトリ
src = ["my_package", "tests"]

# 1行の最大文字数
line-length = 118

[tool.ruff.lint]
select = [
  "F", # pyflakes
  "E", # pycodestyle
  "W", # pycodestyle warnings
  "I", # isort
  "D", # pydocstyle
]

ignore = [
  "D100", # undocumented-public-module
  "D104", # undocumented-public-package
  "D415", # ends-in-punctuation
]

extend-ignore = []

[tool.ruff.lint.pydocstyle]
# docstringはgoogle style
convention = "google"
```

`my_package`以外のモジュールを追加した場合、そのディレクトリの名前を`src`に追加してください。

今後、`ruff.ignore`に**無視するべき**エラーのエラーコードを追加する予定です。
`ruff.ignore`にエラーコードを追加した場合、`vscode`のウィンドウをリロードしてください。

### 拡張機能のインストール

`vscode`に`ruff`の拡張機能をインストールしてください。

コマンドパレットで次のコマンドを実行すると、コードを検証または整形できます。

- `Ruff: format document`: ドキュメントの整形
- `Ruff: Format imports`: import文の整形
- `Ruff: Fix all auto-fixable problems`: 自動修正可能な問題を修正

## mypy導入

### 仮想環境にインストール

次の通り仮想環境に`mypy`をインストールします。

```sh
poetry add mypy
```

`mypy`を使用するために、`pyproject.toml`ファイルに次の設定を追加します。

```toml
[tool.mypy]
strict = true
mypy_path = ["my_package", "tests"]   # モジュールディレクトリとテストディレクトリ
```

`my_package`以外のモジュールを追加した場合、そのディレクトリの名前を`src`に追加してください。

インストールしたパッケージに型情報を持つ`stub`ファイル(`*.pyi`)が含まれていない場合、次の通りパッケージ単位で型チェックを無視する設定を`pyproject.toml`ファイルに追加します。

```toml
[[tool.mypy.overrides]]
ignore_missing_imports = true
module = ["shapely.geometry"]
```

### 拡張機能のインストール

`vscode`に`Mypy Type Checker`拡張機能をインストールします。

その後、`vscode`の設定の`Python > Analysis: Type Checking Mode`を`strict`に設定します。

## pre-commit導入

ローカルリポジトリにコミットする前に、リンター、フォーマッター及び型検証を実行するために、`pre-commit`を導入します。

```sh
# 仮想環境にpre-commitをインストール
poetry add pre-commit
# pre-commitの設定ファイルを作成及び編集
code .pre-commit-config.yaml
# pre-commitをgitにインストール
pre-commit install
```

- `.pre-commit-config.yaml`ファイルの内容

```yaml
repos:
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.4.2     # ruffバージョン
    hooks:
      # リンターの実行
      - id: ruff
        name: lint with ruff
      - id: ruff
        name: sort imports with ruff
        args: [--select, I, --fix]
      # フォーマッターの実行
      - id: ruff-format
        name: format with ruff
#  - repo: https://github.com/pre-commit/mirrors-mypy
#    rev: v1.10.0    # mypyバージョン
#    hooks:
#      # タイプチェックの実行
#      - id: mypy
#        name: type check with mypy
#        args: [--strict, --ignore-missing-imports]
```

ローカルリポジトリにコミットするとき、実際にコミットされる前に次の通り`pre-commit`が実行されます。

```sh
% git add --all
% git commit -m "Implement the sample program."
lint with ruff...........................................................Passed
sort imports with ruff...................................................Passed
format with ruff.........................................................Passed
type check with mypy.....................................................Passed
[main 0075caa] Implement the sample program.
 5 files changed, 344 insertions(+)
 create mode 100644 .pre-commit-config.yaml
 create mode 100644 .vscode/settings.json
 create mode 100644 my_package/__main__.py
 create mode 100644 poetry.lock
```

## `vscode`のエディタ設定

`vscode`のエディタ設定を次の通り設定してください。

- ファイルの文字コードは`UTF-8`（デフォルトで`UTF-8`であるため、`UTF-8`以外になっていないか確認）
- ファイルの末尾に改行を挿入
- 行末の空白を削除
- ファイルを保存したときに、自動的にフォーマット
- タブをスペースに展開
- インデント幅を4に設定
- デフォルトのフォーマッターは`charliermarsh.ruff`
- ファイルを保存したときのアクション
  - 自動的にすべての問題を修正
  - 自動的に`import`文を整理

`vscode`に次の設定が指定されているか確認してください。

```json
{
  "files.insertFinalNewline": true,
  "files.trimTrailingWhitespace": true,
  "editor.formatOnSave": true,
  "editor.insertSpaces": true,
  "[python]": {
    "editor.tabSize": 4,
    "editor.defaultFormatter": "charliermarsh.ruff",
    "editor.codeActionsOnSave": {
      "source.fixAll": "explicit",
      "source.organizeImports": "explicit",
    }
  },
}
```

### 改行コードについて

改行コードは`LF`に統一してください。
ただし、`OSS`に貢献する場合があり、その場合`OSS`のルールに従う必要があります。
よって、`vscode`で改行コードを`LF`に制限していません。

## その他`vscode`拡張機能の導入

`vscode`に次の拡張機能をインストールしてください。

- `Python`
- `autoDocstring`

`autoDocstring`は、クラスまたは関数定義の下で`"""`を入力すると、`docstring`を自動的に挿入します。

<https://marketplace.visualstudio.com/items?itemName=njpwerner.autodocstring>

![autoDocstring](https://github.com/NilsJPWerner/autoDocstring/raw/master/images/demo.gif)

## 実装方針

可能な限り[PEP8](https://pep8-ja.readthedocs.io/ja/latest/)に従い、コードを記述してください。
上記を設定した場合、`ruff`がPEP8に準拠しているか、自動的に検証して整形します。

### `docstring`と型注釈

- 可能な限り関数やメソッドの引数及び戻り値の型を`type hint`で注釈（アノテーション）すること
- 関数やクラスの説明を`docstring`で記述すること
- 関数やメソッドの引数及び戻り値の説明を`docstring`で記述すること
- ファイルレベルの変数を定義する場合、その変数の用途を`docstring`で記述して、型を明記すること

### 関数やメソッドの引数や戻り値の型

- データ構造を関数やメソッドの引数や戻り値に使用する場合、[dataclass](https://docs.python.org/3/library/dataclasses.html)や[enum](https://docs.python.org/3/library/enum.html)を使用すること
  - 辞書やタプルの使用は関数やメソッドのスコープ内に限定して、関数やメソッドの仕様を明確にする

### 型アノテーションについて

- 型アノテーションは、[Type hints cheat sheet](https://mypy.readthedocs.io/en/stable/cheat_sheet_py3.html)を参照して記述すること
- サードパーティーのライブラリの型アノテーションに頑張り過ぎないこと
  - 注釈が難しい場合は`Any`型で注釈することも可

### テスト

- 単体テスト及び統合テストを実装すること
- 単体テスト及び統合テストは、そのテストで何を検証しているか`docstring`で記述すること
- 単体テスト及び統合テストの名前（関数名など）は、上記説明を表現したものにすること
- 単体テストでは1つの振る舞いを検証すること
- 各テストは実装の詳細を検証せず、観測可能な振る舞いを検証すること（ブラックボックステスト）
- テストは限界値テストなどの慣習に従うこと

## 実装例

### `my_package`プログラムを実装する例

`my_package`ディレクトリに、プログラムのエントリポイントとなる`__main__.py`ファイルを作成して、次の通り実装します。

```sh
code my_package/__main__.py
```

```python
# my_package/__main__.py
if __name__ == "__main__":
    print("Hello, my-package!")
```

サンプルプログラムは次の通り実行できます。

```sh
poetry run python -m my_package
```

### プロジェクトにモジュールと別プログラムを実装する例

プロジェクトディレクトリに`common`と`other_package`ディレクトリを作成して、次の通り実装します。

```sh
mkdir common
code common/__init__.py
mkdir other_package
touch other_package/__init__.py
code other_package/__main__.py
```

```python
# common/__init__.py
def message() -> str:
    """メッセージを返す。

    Returns:
        str: メッセージ
    """
    return "Hello, common!"
```

```python
# other_package/__main__.py
from common import message

if __name__ == "__main__":
    print("Hello, other-package!")
    print(message())
```

`other_package`プログラムは次の通り実行できます。

```sh
poetry run python -m other_package
```

モジュールと別プログラムを実装した後のファイルシステム構成を次に示します。

```text
.
├── .git
├── .gitignore
├── .mypy_cache
├── .pre-commit-config.yaml
├── .python-version
├── .ruff_cache
├── .vscode
├── README.md
├── common
│   ├── __init__.py
│   └── __pycache__
├── my_package
│   ├── __init__.py
│   ├── __main__.py
│   └── __pycache__
├── other_package
│   ├── __init__.py
│   ├── __main__.py
│   └── __pycache__
├── poetry.lock
├── pyproject.toml
└── tests
    └── __init__.py
```
