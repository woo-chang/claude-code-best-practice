# macOS 설치

[Day 0으로 돌아가기](README.md)

---

**터미널**
- 터미널을 엽니다 (`Cmd + Space` 누르고 "Terminal" 입력 후 Enter)

**Homebrew**
- Homebrew가 이미 설치되어 있는지 확인:
  ```bash
  brew --version
  ```
- "command not found"가 표시되면 Homebrew를 먼저 설치하세요:
  ```bash
  /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
  ```

**Claude Code**
- ```bash
  brew install --cask claude-code
  ```

**설치 확인**
- ```bash
  claude --version
  ```

---

이제 [README.md](README.md)로 돌아가서 인증 설정을 진행하세요.
