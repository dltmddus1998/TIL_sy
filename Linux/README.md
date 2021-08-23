# 사용 권한

폴더와 파일의 권한으로 폴더인지 파일인지 구분하는 방법과, 폴더나 파일의 사용 권한을 변경하는 방법

### **Achivement Goals**

---

- 사용 권한과 소유자에 대해 이해하고, 사용 권한을 변경할 수 있다.

  - 파일의 소유자와 파일에 적용된 사용 권한을 확인하고 이해할 수 있다. `ls -l`

    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/84fecdfc-cca5-4fba-ba2d-0b59b0d9de22/Untitled.png)

    - 폴더 linux는 `drwxr-xr-x` 라고 출력되었다.
      - 첫 시작
        - `-` : not directory (파일)
        - `d` : directory (폴더)
      - `r` : read permission
      - `w` : write permission
      - `x` : execute permission
        - 세 번에 걸쳐 나타나는 이유 : 사용자와 그룹, 나머지에 대한 권한을 표시하기 때문에.
      - helloworld.js의 경우 소유자는 읽기와 쓰기, 다른 사용자 그룹은 읽기만 가능하다는 의미이다.

    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/58588478-5099-4ccb-be0e-953a26b4b29b/Untitled.png)

  - 파일에 적용된 사용 권한을 변경할 수 있다. `chmod`

    - user, group, and other
      - user : 파일의 소유자, 기본적으로 파일을 만든 사람이 소유자가 된다.
      - group : group에는 여러 user 포함 가능. 그룹에 속한 모든 user는 파일에 대한 동일한 group 엑세스 권한을 갖는다.
      - other : 파일에 대한 엑세스 권한이 있는 다른 user. 파일을 만들지 않은 다른 모든 user. other 권한을 설정하면, 해당 권한을 global 권한 설정이라고 볼 수 있다.
    - `chmod`

      - 명령어 `chmod` 폴더나 파일의 읽기, 쓰기, 실행 권한을 변경할 수 있다.
      - **OS에 로그인한 사용자와, 폴더나 파일의 소유자가 같을 경우**에 명령어 `chmod` 로 폴더나 파일의 **권한을 변경**할 수 있다.
      - **OS에 로그인한 사용자와, 폴더나 파일의 소유자가 다를 경우**에는 관리자 권한을 획득하는 **명령어 `sudo` 를 이용**해 폴더나 파일의 권한을 변경할 수 있다.
      - `chmod` 사용법 두 가지

        - Symbolic method : 더하기 (`+`), 빼기 (`-`), 할당 (`=`)과 엑세서 유형을 표기해서 변경하기
          - Symbolic method는 엑세스 클래스, 연산자, 엑세스 타입으로 구분한다.

        [Symbolic method의 구분](https://www.notion.so/619600e16e164e93aef11482b641b447)

        ```sql
        chmod g-r filename # removes read permission from group
        chmod g+r filename # adds read permission to group
        chmod g-w filename # removes write permission from group
        chmod g+w filename # adds write permission to group
        chmod g-x filename # removes execute permission from group
        chmod g+x filename # adds execute permission to group
        chmod o-r filename # removes read permission from other
        chmod o+r filename # adds read permission to other
        chmod o-w filename # removes write permission from other
        chmod o+w filename # adds write permission to other
        chmod o-x filename # removes execute permission from other
        chmod o+x filename # adds execute permission to other
        chmod u+x filename # adds execute permission to user
        ```

        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0f617b0f-c59c-461b-af07-0c6c920414d7/Untitled.png)

        실제로 helloworld.js 의 권한을 위와 같이 변경할 수 있었다.

        - Absolute form : rwx를 3bit로 해석하여, 숫자 3자리로 권한을 표기해서 변경하기

          - Absolute form은 숫자 7까지 나타내는 3 bits의 합으로 표기한다.
          - 사용자, 그룹, 또는 다른 사용자나 그룹마다 `rwx` 가 나타나고, 각 영역의 boolean 값으로 표기할 수 있다.

          [Absolute form 구분](https://www.notion.so/207e126382fa40ca97967576ec480b85)

          user는 `rwx` 를, group과 other은`r--` 로 권한을 변경하려고 한다면, 위표에 나와있는 숫자의 합을 user, group, other 순으로 입력하여 사용한다.

          ```sql
          # u=rxw (4 + 2 + 1 = 7), go=r (4 + 0 + 0 = 4)
          chmod 744 helloworld.js
          ```

          ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4afcfd20-0887-4951-88ff-f739a786eb73/Untitled.png)

# 환경 변수

API key와 같이 공개할 수 없는 정보가 코드에 포함될 경우, 네트워크를 통해 API key가 공개될 수 있다. 이런 일을 방지하기 위해서는, API key를 PC에 저장해두고 사용해야 한다. JavaScript에서 변수에 문자열을 할당하는 것처럼, API key를 PC에서 사용하는 변수에 할당하고 사용할 수 있다.

JavaScript에서 변수는 스코프에 따라 전역변수와 지역변수로 나뉜다. 그러나 파일 A에서 전역변수로 선언하더라도, 다른 파일 B에서 파일 A의 전역변수에 접근할 수 없다. 그러나 파일 A의 전역변수를 `export` 한다면, 파일 B에서 파일 A의 전역변수에 접근할 수 있다.

Linux 기반의 운영체제의 PC에는 시스템 자체에 전역변수를 설정할 수 있다. 그리고 시스템에 설정한 전역변수를 환경변수라고 한다. `export` 를 이용해 환경변수를 설정할 수 있다.

### **Achivement Goals**

---

- PC에 저장하는 환경변수가 무엇인지 이해하고, 사용할 수 있다.

  - PC에 저장된 환경변수를 확인할 수 있다. `export`

    ```sql
    $ export

    HOME=/Users/syl
    HOMEBREW_CELLAR=/opt/homebrew/Cellar
    HOMEBREW_PREFIX=/opt/homebrew
    HOMEBREW_REPOSITORY=/opt/homebrew
    INFOPATH=/opt/homebrew/share/info:
    LANG=ko_KR.UTF-8
    LESS=-R
    LOGNAME=syl
    LSCOLORS=Gxfxcxdxbxegedabagacad
    LaunchInstanceID=1C5CA6F7-8720-498E-81A0-BDBBF1D381F8
    MANPATH=/opt/homebrew/share/man::
    OLDPWD=/Users/syl/Desktop
    PAGER=less
    PATH=/opt/homebrew/bin:/opt/homebrew/sbin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin
    PWD=/Users/syl
    SECURITYSESSIONID=186b8
    SHELL=/bin/zsh
    SHLVL=1
    SSH_AUTH_SOCK=/private/tmp/com.apple.launchd.wubjnkEqXQ/Listeners
    TERM=xterm-256color
    TERM_PROGRAM=Apple_Terminal
    TERM_PROGRAM_VERSION=440
    TERM_SESSION_ID=A90A97C3-785E-47A8-892B-D0F14C113726
    TMPDIR=/var/folders/8v/nhz3r9cn5dg323cm8kd0sfvr0000gn/T/
    USER=syl
    XPC_FLAGS=0x0
    XPC_SERVICE_NAME=0
    ZSH=/Users/syl/.oh-my-zsh
    __CFBundleIdentifier=com.apple.Terminal
    ```

    ```sql
    $ export urclass="is good"
    $ echo $urclass
    is good
    ```

  - PC에 저장된 환경변수를 불러올 수 있다. `dotenv`

    - npm 모듈 dotenv를 통해 자바스크립트에서 환경변수 사용 가능하다.
    - npm 모듈을 설치하고 사용하기 위해서, 새로운 폴더를 만들고 `npm init` 을 입력한다.
    - `npm i dotenv` 를 입력해 모듈을 설치한다.
      - **이 때 생성된 폴더는 설치할 모듈과 이름이 같아서는 안된다.**

    ```sql
    $ mkdir environment_variable
    $ cd environment_variable
    $ npm init # 엔터키를 여러번 입력해 init을 마친다.
    $ npm i dotenv # dotenv 모듈 설치
    ```

    ```sql
    ➜  environment_variable git:(master) ✗ nano index.js
    ➜  environment_variable git:(master) ✗ cat index.js
    console.log(process.env)
    ➜  environment_variable git:(master) ✗ node index.js
    {
      __CFBundleIdentifier: 'com.apple.Terminal',
      TMPDIR: '/var/folders/8v/nhz3r9cn5dg323cm8kd0sfvr0000gn/T/',
      XPC_FLAGS: '0x0',
      LaunchInstanceID: '1C5CA6F7-8720-498E-81A0-BDBBF1D381F8',
      TERM: 'xterm-256color',
      SSH_AUTH_SOCK: '/private/tmp/com.apple.launchd.wubjnkEqXQ/Listeners',
      SECURITYSESSIONID: '186b8',
      XPC_SERVICE_NAME: '0',
      TERM_PROGRAM: 'Apple_Terminal',
      TERM_PROGRAM_VERSION: '440',
      TERM_SESSION_ID: 'A90A97C3-785E-47A8-892B-D0F14C113726',
      SHELL: '/bin/zsh',
      HOME: '/Users/syl',
      LOGNAME: 'syl',
      USER: 'syl',
      PATH: '/opt/homebrew/bin:/opt/homebrew/sbin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin',
      SHLVL: '1',
      PWD: '/Users/syl/Desktop/environment_variable',
      OLDPWD: '/Users/syl/Desktop',
      HOMEBREW_PREFIX: '/opt/homebrew',
      HOMEBREW_CELLAR: '/opt/homebrew/Cellar',
      HOMEBREW_REPOSITORY: '/opt/homebrew',
      MANPATH: '/opt/homebrew/share/man::',
      INFOPATH: '/opt/homebrew/share/info:',
      ZSH: '/Users/syl/.oh-my-zsh',
      PAGER: 'less',
      LESS: '-R',
      LSCOLORS: 'Gxfxcxdxbxegedabagacad',
      urclass: 'is good',
      LANG: 'ko_KR.UTF-8',
      _: '/opt/homebrew/bin/node',
      __CF_USER_TEXT_ENCODING: '0x1F5:0x3:0x33'
    }
    ```

    설치한 dotenv를 이용해 위와 같이 환경변수에 접근할 수 있다. 새로운 파일 index.js를 생성한 후 `console.log(process.env)` 를 입력해준다. Node.js의 내장 객체 `process.env` 를 이용하면, 명령어 `export` 로 확인한 내용과 동일한 내용을 객체로 출력한다.

  - Node.js에서 환경변수를 영구적용할 수 있다. `.env`

    - 명령어 `export` 로 적용한 환경변수는 **현재 사용 중인 터미널에서만 임시로 사용이 가능하다.**
    - 환경변수를 Linux 운영체제에 저장하는 방법은 여러 가지가 있지만, **Node.js에서는 파일 .env를 만들어 저장하는 방법을 사용한다.**

      - 먼저 .env 파일 생성 후, 사용하고자 하는 환경변수를 입력한 뒤 저장한다.

      ```sql
      ➜  environment_variable git:(master) ✗ nano .env
      ➜  environment_variable git:(master) ✗ cat .env
      myname=kimcoding
      ```

      모듈 dotenv를 이용하면, 파일 .env에 저장한 환경변수를 조회할 수 있다. index.js를 다음과 같이 수정하고, 저장한다.

      ```sql
      ➜  environment_variable git:(master) ✗ nano index.js
      ➜  environment_variable git:(master) ✗ cat index.js
      const dotenv = require("dotenv");
      dotenv.config();
      console.log(process.env.myname);
      ```

      이후 index.js 실행하면 다음과 같다.

      ```sql
      ➜  environment_variable git:(master) ✗ node index.js
      kimcoding
      ```

      환경변수를 이용해 API key, DB password와 같이 민감한 정보를 저장하고 관리할 수 있다. 그 뿐만 아니라 서로 다른 PC 또는 어러 .env 파일에서, 같은 변수 이름에 다른 값을 할당할 수 있다.

      실제 제품(서비스)을 개발하는 과정에는 **개발 환경**(local or development etc.)과 **테스트 서버의 환경**(test), 그리고 **실제 제품을 제공하는 환경**(production)이 있다. 예를 들어 구글 API를 이용해 웹 애플리케이션을 만들 경우, 개발 환경에서는 개발자 개인의 API 키를 이용할 수 있다. 그러나 제품을 서비스할 때에 개인 API 키를 사용하면, 일일 요청량을 초과하는 경우 제품이 정상적인 동작을 할 수 없다. 이런 경우를 방지하기 위해 실제 제품에서는 기업용 API를 사용한다. 이처럼 **개발 환경과 제품을 제공하는 환경에서 사용하는 API 키가 다른 경우, 환경변수를 이용해 환경을 구분하여 코드를 작성할 수 있다**.

      데이터베이스도 마찬가지로 개발, 테스트, 제품 환경으로 구분할 수 있다.

      ```sql
      # 개발환경에서 접근할 데이터베이스
      DATABASE_NAME=my_app_dev

      # 테스트환경에서 접근할 데이터베이스
      DATABASE_NAME=my_app_test

      # 제품환경에서 접근할 데이터베이스
      DATABASE_NAME=my_app_production
      ```
