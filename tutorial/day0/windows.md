# Windows 설치

[Day 0으로 돌아가기](README.md)

---

**Node.js**
- [nodejs.org](https://nodejs.org)로 이동
- **"Download Node.js (LTS)"** 버튼 클릭 — `.msi` 설치 파일이 다운로드됩니다
- `.msi` 파일을 실행하고 마법사에서 **Next**를 클릭
- 기본값을 수락하고 **Install** 클릭, 완료될 때까지 기다립니다

**Node.js 설치 확인**
- **새** 터미널(PowerShell 또는 Windows Terminal)을 열고 실행:
  ```powershell
  node --version
  npm --version
  ```

**Claude Code**
- ```powershell
  npm install -g @anthropic-ai/claude-code
  ```
- 권한 오류가 발생하면, 터미널을 **관리자 권한으로 실행**하세요 (마우스 오른쪽 클릭 > 관리자 권한으로 실행)

**설치 확인**
- ```powershell
  claude --version
  ```

---

이제 [README.md](README.md)로 돌아가서 인증 설정을 진행하세요.
