# Linux 설치

[Day 0으로 돌아가기](README.md)

## 사전 요구 사항

**Node.js v18 이상**과 **npm**이 필요합니다.

## 1단계: Node.js 설치

### 옵션 A: nodejs.org 다운로드 페이지에서 fnm 사용 (권장)

**fnm** (Fast Node Manager)은 Node.js에서 공식 권장하는 방법입니다. 빠르고 가벼우며, 필요 시 Node 버전을 쉽게 전환할 수 있습니다.

1. 브라우저를 열고 [nodejs.org/en/download](https://nodejs.org/en/download)로 이동하세요.

2. **"Get Node.js® vXX.XX.X (LTS) for __ using __ with __"**라는 드롭다운 행이 표시됩니다. 드롭다운을 다음과 같이 설정하세요:

   | 드롭다운 | 선택 |
   |----------|--------|
   | Version | **vXX.XX.X (LTS)** — 기본 LTS 버전 유지, 변경하지 마세요 |
   | OS | **Linux** |
   | Package Manager | **fnm** ("Recommended (Official)" 아래) |
   | Package Format | **npm** — 기본값 유지 |

3. 페이지에 실행할 명령어가 표시됩니다. 터미널을 열고 복사-붙여넣기 하세요. 대략 다음과 같습니다:

   ```bash
   # 1단계 — fnm 설치
   curl -fsSL https://fnm.vercel.app/install | bash

   # 2단계 — 터미널 재시작 또는 셸 프로파일 다시 로드
   source ~/.bashrc   # 또는: source ~/.zshrc (zsh 사용 시)

   # 3단계 — Node.js 설치
   fnm install 24   # 페이지에 정확한 버전 번호가 표시됨
   ```

   > 위의 버전 번호는 다를 수 있습니다 — 항상 웹사이트에 표시된 버전을 사용하세요.

4. **터미널을 닫고 다시 열거나** 위의 `source` 명령을 실행하여 `fnm`, `node`, `npm`을 사용할 수 있게 합니다.

> **fnm을 사용하는 이유?** Node.js 다운로드 페이지의 "Recommended (Official)" 카테고리에 있습니다. nvm과 마찬가지로 홈 디렉토리에 Node를 설치하므로 npm 글로벌 설치에 `sudo`가 필요 없습니다 — 하지만 fnm은 훨씬 빠르며(Rust로 작성됨) Windows, macOS, Linux에서 동일하게 작동합니다.

### 옵션 B: 배포판 패키지 관리자 사용

더 빠르지만 오래된 버전의 Node.js가 설치될 수 있습니다. **설치 후 버전 확인** — v18 미만이면 옵션 A를 대신 사용하세요.

**Ubuntu / Debian:**

```bash
sudo apt update
sudo apt install -y nodejs npm

# 버전 확인
node --version   # v18 이상이어야 함
```

**Fedora:**

```bash
sudo dnf install -y nodejs npm
```

**Arch Linux:**

```bash
sudo pacman -S nodejs npm
```

### 옵션 C: NodeSource (apt를 통한 최신 LTS, nvm 없이)

nvm 없이 최신 LTS를 원하는 Ubuntu/Debian 사용자:

```bash
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt install -y nodejs
```

## 2단계: Node.js 설치 확인

```bash
node --version
npm --version
```

두 명령 모두 버전 번호가 출력되어야 합니다. `node --version`은 v18.x 이상을 표시해야 합니다.

## 3단계: Claude Code 설치

```bash
npm install -g @anthropic-ai/claude-code
```

> **권한 오류?**
> - **fnm** 또는 **nvm** 사용 시: 이 오류가 발생하면 안 됩니다. fnm/nvm이 활성화되어 있는지 확인하세요 (`which node`가 홈 디렉토리 내 경로를 가리켜야 하며, `/usr/...`가 아니어야 합니다).
> - 시스템 설치를 사용한 경우: `sudo npm install -g @anthropic-ai/claude-code`를 사용하거나, npm 글로벌 디렉토리 권한을 수정하세요:
>   ```bash
>   mkdir -p ~/.npm-global
>   npm config set prefix '~/.npm-global'
>   echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc
>   source ~/.bashrc
>   ```

## 4단계: Claude Code 설치 확인

```bash
claude --version
```

Claude Code 버전이 출력되어야 합니다. 이제 [README.md](README.md)로 돌아가서 인증 설정을 진행하세요.

---

## 참고 사항

- **WSL (Windows Subsystem for Linux):** 이 가이드는 WSL 내부에서도 작동합니다. WSL 터미널에서 동일한 단계를 따르세요.
- **PATH 문제:** 설치 후 `claude`를 찾을 수 없는 경우, npm 글로벌 bin이 PATH에 있는지 확인하세요. `npm config get prefix`를 실행 — 해당 경로의 `bin/` 하위 디렉토리가 PATH에 있어야 합니다.
