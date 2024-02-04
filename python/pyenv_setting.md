## Pyenv setting 방법 (VS Code)

`brew install pyenv`

`code ~/.zshrc`

.zshrc 안에

```
export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init --path)"
eval "$(pyenv init -)"
```



```
# 설치 가능한 Python 버전
$ pyenv install --list

# 특정한 버전 Python 설치
$ pyenv install 3.9.0

# 특정한 버전 Python 삭제
$ pyenv uninstall 3.9.0

# 설치된 Python list
$ pyenv versions

# 해당 Python 버전을 기본으로 설정
$ pyenv global 3.9.0
```