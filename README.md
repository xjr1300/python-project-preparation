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
    - [`import`文](#import文)
    - [`docstring`と型注釈](#docstringと型注釈)
    - [関数やメソッドの引数や戻り値の型](#関数やメソッドの引数や戻り値の型)
    - [型アノテーションについて](#型アノテーションについて)
    - [テスト](#テスト)
  - [実装例1](#実装例1)
    - [`my_package`プログラムを実装する例](#my_packageプログラムを実装する例)
    - [プロジェクトにモジュールと別プログラムを実装する例](#プロジェクトにモジュールと別プログラムを実装する例)
  - [実装例2](#実装例2)
    - [calcプロジェクトの作成と設定](#calcプロジェクトの作成と設定)
    - [演算する関数の実装](#演算する関数の実装)
    - [コマンドラインインターフェースの作成](#コマンドラインインターフェースの作成)
    - [エントリポイントの作成](#エントリポイントの作成)
    - [プログラムの実行例](#プログラムの実行例)
    - [リポジトリに変更をコミット](#リポジトリに変更をコミット)
  - [実装例3](#実装例3)
    - [calc2プロジェクトの作成と設定](#calc2プロジェクトの作成と設定)
    - [コマンドラインインターフェースとエントリポイントの実装](#コマンドラインインターフェースとエントリポイントの実装)
    - [プログラムの実行例](#プログラムの実行例-1)
    - [リポジトリに変更をコミット](#リポジトリに変更をコミット-1)

## 方針

- `poetry`でプロジェクト及びパッケージの依存関係を管理
- `ruff`でコードを検証及び整形
- `mypy`でタイプセーフなコードを記述することを目指す（あくまで目標）
- `pre-commit`でリポジトリにコミットする前にコードを整形（将来的には静的型検査もしたい）

`poetry`については、[ここ](https://python-poetry.org/docs/)を参照してください。

`ruff`については、[ここ](https://docs.astral.sh/ruff/)を参照してください。

`mypy`については、[ここ](https://mypy.readthedocs.io/en/stable/)を参照してください。

`pre-commit`については、[ここ](https://pre-commit.com/)を参照してください。

`ruff`と`pre-commit`の連携については、[ここ](https://github.com/astral-sh/ruff-pre-commit)を参照してください。

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

ここでは、[`poetry`標準のプロジェクトディレクトリ構成](https://python-poetry.org/docs/basic-usage/#project-setup)を採用します。

作成したプロジェクトのモジュールは、[`-m`オプションを使用することで実行](https://docs.python.org/ja/3/using/cmdline.html#cmdoption-m)できます。

```sh
# poetryを使用しないときの実行方法
python -m <module name>
# poetryを使用するときの実行方法
poetry run python -m <module name>
```

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
code .gitignore
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

# ターゲットバージョン
target-version = "py312"

# モジュールディレクトリとテストディレクトリ
src = ["my_package", "tests"]

# 1行の最大文字数
line-length = 88

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

その後、`vscode`の設定を開いて`Python > Analysis: Type Checking Mode`を`strict`に設定します。

## pre-commit導入

ローカルリポジトリにコミットする前に、リンター、フォーマッター（または型検証）を実行するために、`pre-commit`を導入します。

```sh
# 仮想環境にpre-commitをインストール
poetry add pre-commit
# pre-commitの設定ファイルを作成及び編集
code .pre-commit-config.yaml
# pre-commitをgitにインストール
poetry run pre-commit install
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
% git commit -m "Build the environment."
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

`vscode`のエディタ設定を次の通り設定します。

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

`vscode`のコマンドパレットを開いて、`Preferences: Open Settings (JSON)`を入力／選択して、ユーザー設定ファイルを表示します。
ユーザー設定ファイルに、次に示した設定が存在するか確認してください。

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

改行コードは`LF`にしてください。

ただし、`OSS`に貢献する場合、`OSS`のルールに従う必要があるため、`vscode`で改行コードを`LF`に強制することをしません。

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

### `import`文

- `import`文は、標準ライブラリ、サードパーティーライブラリ、ローカルライブラリの順に記述すること
- 各`import`グループでは、`import`文、`from ... import ...`文の順に記述すること

```python
import io
from datetime import datetime, timedelta

import numpy as np
from django.db import models

from my_package import my_module
```

### `docstring`と型注釈

- **可能な限り**関数やメソッドの引数及び戻り値の型を`type hint`で型注釈（型アノテーション）すること
- 関数やクラスの説明を`docstring`で記述すること
- 関数やメソッドの引数及び戻り値の説明を`docstring`で記述すること
- ファイルレベルの変数を定義する場合、その変数の用途を`docstring`で記述して、型を明記すること

### 関数やメソッドの引数や戻り値の型

- データ構造を関数やメソッドの引数や戻り値に使用する場合、[dataclass](https://docs.python.org/3/library/dataclasses.html)や[enum](https://docs.python.org/3/library/enum.html)を使用すること
  - 辞書やタプルの使用は関数やメソッドのスコープ内に限定して、関数やメソッドの仕様を明確にすること
  - 妥当な理由で引数や戻り値の型を辞書やタプルを使用する場合、辞書やタプルの使用を`docstring`で説明すること

### 型アノテーションについて

- 型アノテーションは、[Type hints cheat sheet](https://mypy.readthedocs.io/en/stable/cheat_sheet_py3.html)を参照して記述すること
- サードパーティーのライブラリの型アノテーションに頑張り過ぎないこと
  - 注釈が難しい場合は`Any`型で注釈することも可
- `pandas.DataFrame`など、型アノテーションしてもデータの仕様が明確でない場合、データの使用を`docstring`で説明すること
  - `pandas.DataFrame`の場合、列名やデータ型などを説明すること

### テスト

- 単体テスト及び統合テストを実装すること
- 単体テスト及び統合テストは、そのテストで何を検証しているか`docstring`で記述すること
- 単体テスト及び統合テストの名前（関数名など）は、上記説明を表現したものにすること
- 単体テストでは1つの振る舞いを検証すること
- 各テストは実装の詳細を検証せず、観測可能な振る舞いを検証すること（ブラックボックステスト）
- テストは限界値テストなどの慣習に従うこと

## 実装例1

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

最後に、`pyproject.toml`を編集して、`common`モジュールと`other_package`モジュールを`ruff`のリント及びフォーマット対象にします（`mypy`の検証対象にします）。

```toml
# pyproject.toml

# モジュールディレクトリとテストディレクトリ
-src = ["my_package", "tests"]
+src = ["common", "my_package", "other_package", "tests"]

# ...snip...

 [tool.mypy]
 strict = true
-mypy_path = ["my_package", "tests"]
-mypy_path = ["common", "my_package", "other_package", "tests"]
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

ファイルシステムを構成後、リポジトリにコミットします。

```sh
git add --all
git commit -m "Implement the my-package."
```

## 実装例2

前回実装した`my_package`プロジェクトでは、`my_package`と`other_package`を実行できるようにしました。

しかし、2つのプログラムを実装した後、リンター及びフォーマッターまたは静的型検証を実行するために、`pyproject.toml`を編集する必要がありました。
もし、`pyproject.toml`を編集することを忘れた場合、リンターなどの対象外になり、リポジトリをチームで共有する場合に問題が発生する可能性があります。

これを解決するために、標準ライブラリの`argparse`モジュールを使用して、1つのプログラムで複数の処理をできるように実装します。

これにより、実装例1では、`ruff`や`mypy`の対象となるディレクトリを追加しましたが、今回は追加する必要がなくなります。

### calcプロジェクトの作成と設定

`calc`プロジェクトを作成して、`calc`プロジェクトを次の通り設定します。

```sh
#
# プロジェクト作成
#
# calcプロジェクトを作成
poetry new calc
# calcディレクトリに移動
cd calc
# calcディレクトリをvscodeで開く
code .
# calcプロジェクトで使用するpythonバージョンを指定
pyenv local 3.12.0
# git除外ファイルの作成（内容は上記同様）
code .gitignore
# calcリポジトリの作成及び初期化
git init
# 変更をステージング
git add --all
# ステージングした内容をリポジトリにコミット
git commit -m "初期コミット"

#
# プロジェクトの環境設定
#
# ruff, mypy, pre-commitを仮想環境にインストール（同時に仮想環境が作成される）
poetry add ruff mypy pre-commit
# pyproject.tomlファイルを編集（内容は上記の`my_package`を`calc`に変更
code pyproject.toml
# .pre-commit-config.yamlファイルを作成及び編集（内容は上記同様）
code .pre-commit-config.yaml
# pre-commitをgitにインストール
poetry run pre-commit install
# 変更をステージング
git add --all
# ステージングした内容をリポジトリにコミット
git commit -m "プロジェクトの環境を設定"
```

### 演算する関数の実装

整数を加算、減算、乗算、整数除算する関数をそれぞれモジュールに実装します。

```sh
# モジュールディレクトリを作成
mkdir calc/{add,sub,mul,div}
# 上記で作成したディレクトリがモジュールとして認識されるように__init__.pyファイルを作成
touch calc/{add,sub,mul,div}/__init__.py
```

それぞのモジュールの`__init__.py`ファイルに次の通り実装します。

```python
# calc/add/__init__.py
def add(x: int, y: int) -> int:
    """整数を加算する。

    Args:
        x (int): 演算子の左辺の値
        y (int): 演算子の右辺の値

    Returns:
        int: 加算した結果結果
    """
    return x + y
```

```python
# calc/sub/__init__.py
def sub(x: int, y: int) -> int:
    """整数を減算する。

    Args:
        x (int): 演算子の左辺の値
        y (int): 演算子の右辺の値

    Returns:
        int: 減算した結果結果
    """
    return x - y
```

```python
# calc/mul/__init__.py
def mul(x: int, y: int) -> int:
    """整数を乗算する。

    Args:
        x (int): 演算子の左辺の値
        y (int): 演算子の右辺の値

    Returns:
        int: 乗算した結果結果
    """
    return x * y
```

```python
# calc/div/__init__.py
def div(x: int, y: int) -> int:
    """整数を整数除算する。

    Args:
        x (int): 演算子の左辺の値
        y (int): 演算子の右辺の値

    Returns:
        int: 整数除算した結果結果

    Raises:
        ZeroDivisionError
    """
    try:
        return x // y
    except ZeroDivisionError:
        raise
```

演算する関数を実装後、変更内容をステージングしてリポジトリにコミットします。

```sh
git add --all
git commit -m "add, sub, mul, divモジュールを実装"
```

### コマンドラインインターフェースの作成

`calc/__main__.py`ファイルを作成して、コマンドラインインターフェースを次の通り実装します。

```python
# calc/__main__.py
import argparse


def create_parser() -> argparse.ArgumentParser:
    """コマンドラインを解析するオブジェクトを作成して返す。

    Returns:
        argparse.ArgumentParser: コマンドラインパーサー
    """
    parser = argparse.ArgumentParser(
        prog="CLI Calculator",
    )
    parser.add_argument("operator", type=str, choices=("add", "sub", "mul", "div"), help="Select operator")
    parser.add_argument("left", type=int, help="left-hand operand")
    parser.add_argument("right", type=int, help="right-hand operand")
    return parser
```

コマンドラインインターフェースを実装した後、変更内容をステージングしてリポジトリにコミットします。

```sh
git add --all
git commit -m "コマンドラインを解析するオブジェクトを実装"
```

### エントリポイントの作成

`calc/__main__.py`ファイルに次の通りエントリポイントを実装します。

```python
 import argparse

+from .add import add
+from .div import div
+from .mul import mul
+from .sub import sub

...snip...


+def main(operator: str, left: int, right: int) -> None:
+    """2つの整数を演算した結果を標準出力に出力する。
+
+    Args:
+        operator (str): 演算子
+        left (int): 演算子の左辺値
+        right (int): 演算子の右辺値
+    """
+    if operator == "add":
+        sign = "+"
+        result = add(left, right)
+    elif operator == "sub":
+        sign = "-"
+        result = sub(left, right)
+    elif operator == "mul":
+        sign = "*"
+        result = mul(left, right)
+    else:
+        sign = "//"
+        result = div(left, right)
+    print(f"{left} {sign} {right} = {result}")
+
+
+if __name__ == "__main__":
+    parser = create_parser()
+    args = parser.parse_args()
+    main(args.operator, args.left, args.right)
```

### プログラムの実行例

```sh
% poetry run python -m calc add 5 2
5 + 2 = 7
% poetry run python -m calc sub 5 2
5 - 2 = 3
% poetry run python -m calc mul 5 2
5 * 2 = 10
% poetry run python -m calc div 5 2
5 // 2 = 2
% poetry run python -m calc div 5 0
Traceback (most recent call last):
  File "<frozen runpy>", line 198, in _run_module_as_main
  File "<frozen runpy>", line 88, in _run_code
  File "/Users/xjr1300/examples/calc/calc/__main__.py", line 50, in <module>
    main(args.operator, args.left, args.right)
  File "/Users/xjr1300/examples/calc/calc/__main__.py", line 43, in main
    result = div(left, right)
             ^^^^^^^^^^^^^^^^
  File "/Users/xjr1300/examples/calc/calc/div/__init__.py", line 15, in div
    return x // y
           ~~^^~~
ZeroDivisionError: integer division or modulo by zero
```

### リポジトリに変更をコミット

```sh
git add --all
git commit -m "エントリポイントを実装"
```

## 実装例3

`argparse`モジュールのサブコマンドを利用すると、サブコマンドごとに異なる引数を定義できます。

[サブコマンド](https://docs.python.org/ja/3/library/argparse.html#sub-commands)

サブコマンドを利用することで`django`の`manage.py`スクリプトのようなコマンドラインインターフェースを実装できます。

ここでは、引数を1つ受け取りフィボナッチ数を計算するサブコマンドと、引数を2つ受け取りそれらの最大公約数を計算するサブコマンドを実装します。

### calc2プロジェクトの作成と設定

`calc`プロジェクトと同様に`calc2`プロジェクトを作成してください。

### コマンドラインインターフェースとエントリポイントの実装

`calc2/__main__.py`ファイルに次の通りコマンドラインインターフェースとエントリポイントを実装します。

```python
import math
from argparse import ArgumentParser, Namespace


def fibonacci(n: int) -> int:
    """フィボナッチ数を計算する。

    Args:
        n (int): フィボナッチ数の項

    Returns:
        int: フィボナッチ数
    """
    if n == 0:
        return 0
    elif n == 1:
        return 1
    return fibonacci(n - 1) + fibonacci(n - 2)


def calc_fibo(args: Namespace) -> None:
    """フィボナッチ数を計算して、その結果を標準出力に出力する。

    Args:
        args (Namespace): コマンドライン引数
    """
    result = fibonacci(args.number)
    print(f"fibonacci({args.number}) = {result}")


def calc_gcd(args: Namespace) -> None:
    """2つの整数の最大公約数を計算して、その結果を標準出力に出力する。

    Args:
        args (Namespace): コマンドライン引数
    """
    result = math.gcd(args.x, args.y)
    print(f"gcd({args.x}, {args.y}) = {result}")


def create_parser() -> ArgumentParser:
    """コマンドライン引数を解析するオブジェクトを作成して返す。

    python -m calc2 fibo <number>
    python -m calc2 gcd <x> <y>

    Returns:
        ArgumentParser: コマンドライン引数を解析するオブジェクト
    """
    parser = ArgumentParser(prog="CLI Calculator2")
    sub_parsers = parser.add_subparsers(help="Sub-command help")
    # fiboサブコマンド
    fibo_parser = sub_parsers.add_parser("fibo", help="Fibonacci number")
    fibo_parser.add_argument("number", type=int, help="Term of the Fibonacci number")
    fibo_parser.set_defaults(func=calc_fibo)
    # gcdサブコマンド
    gcd_parser = sub_parsers.add_parser("gcd", help="Greatest common divisor")
    gcd_parser.add_argument("x", type=int, help="Greatest common divisor of x")
    gcd_parser.add_argument("y", type=int, help="Greatest common divisor of y")
    gcd_parser.set_defaults(func=calc_gcd)
    return parser


if __name__ == "__main__":
    parser = create_parser()
    args = parser.parse_args()
    args.func(args)
```

### プログラムの実行例

```sh
 % poetry run python -m calc2 fibo 0
fibonacci(0) = 0
 % poetry run python -m calc2 fibo 1
fibonacci(1) = 1
 % poetry run python -m calc2 fibo 10
fibonacci(10) = 55

poetry run python -m calc2 gcd 18 30
gcd(18, 30) = 6
% poetry run python -m calc2 gcd 60 72
gcd(60, 72) = 12
```

### リポジトリに変更をコミット

```sh
git add --all
git commit -m "計算機2を実装"
```
