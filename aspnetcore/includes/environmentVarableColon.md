`:` の区切り記号は、すべてのプラットフォームの環境変数階層キーには対応していません。 `__`（ダブルアンダースコア）は、

* すべてのプラットフォームに対応しています。 たとえば、[Bash](https://linuxhint.com/bash-environment-variables/) は`:` の区切り記号には対応していませんが、`__` には対応しています。
* 自動で `:` に置換されます。