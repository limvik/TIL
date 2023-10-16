# WSL(Windows Subsystem for Linux)에서 Rust 설치 방법

1. curl 설치

- ubuntu/debian

```bash
apt-get install curl
```

다른 배포판은 [Linux에서 컬을 설치하는 방법](https://ko.linux-console.net/?p=549) 참고

2. 다운로드

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

3. 환경변수 갱신

```bash
source "$HOME/.cargo/env"
```

4. 버전 확인

```bash
rustc --version
cargo --version
rustup --version
```

공식 홈페이지([링크](https://www.rust-lang.org/))와 버전 일치여부 확인

5. 삭제 방법

```bash
rustup self uninstall
```

---

출처
- [Getting Started](https://www.rust-lang.org/learn/get-started)
- [Install Rust](https://www.rust-lang.org/tools/install)