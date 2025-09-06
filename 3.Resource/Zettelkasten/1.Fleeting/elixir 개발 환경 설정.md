### asdf 버전 매니저 설치
1. 터미널 실행
	1. zsh로 실행할 것
2. `asdf` 설치
```bash
brew install asdf
```
3. `asdf` 설정
```bash
echo -e "\n. $(brew --prefix asdf)/libexec/asdf.sh" >> ~/.zshrc
source ~/.zshrc
```
4. `asdf` 플러그인 설치
```
asdf plugin add erlang
asdf plugin add elixir
```
5. 환경 변수 설정
```bash
export CC="clang"
export CFLAGS="-O2 -g -I$(brew --prefix openssl@3)/include -I$(brew --prefix unixodbc)/include"
export LDFLAGS="-L$(brew --prefix openssl@3)/lib -L$(brew --prefix unixodbc)/lib"
export CPPFLAGS="-I$(xcrun --show-sdk-path)/usr/include/c++/v1" 
export CXXFLAGS="-stdlib=libc++"
export KERL_CONFIGURE_OPTIONS="--with-ssl=$(brew --prefix openssl@3) --with-odbc=$(brew --prefix unixodbc) --disable-debug --disable-silent-rules --enable-dynamic-ssl-lib --enable-hipe --enable-shared-zlib --enable-smp-support --enable-threads --enable-wx"
```
6. `erlang` 설치 및 설정
```bash
arch -arm64 asdf install erlang latest
asdf set erlang latest # 요건 솔직히 안해도 될 듯?
erl -version # 버전 확인
```
7. `elixir` 설치 및 설정
```bash
arch -arm64 asdf list all elixir # 버전 확인
arch -arm64 asdf install elixir 1.18.4
asdf set elixir 1.18.4 # 요건 솔직히 안해도 될 듯?
elixir --version # 버전 확인
iex --version # 버전 확인
```
8. `iex` 테스트
```bash
iex
Erlang/OTP 28 [erts-16.0.2] [source] [64-bit] [smp:8:8] [ds:8:8:10] [async-threads:1] [jit]

Interactive Elixir (1.18.4) - press Ctrl+C to exit (type h() ENTER for help)
iex(1)> IO.puts "Hello World!"
Hello World!
:ok
# 나갈 때는 Control + C를 두번 연타
```
9. 첫 번째 프로젝트 생성해보기
```bash
mkdir study && cd study
mix new my_first_elixir && cd my_first_elixir
asdf set erlang latest
asdf set elixir 1.18.4
iex -S mix
```
---
참고: VScode 확장 설치
1. ElixirLS
2. vscode-elixir
3. Elixir Formatter
