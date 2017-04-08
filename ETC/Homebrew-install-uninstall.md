#HomeBrew mysql install


## Install

## HomeBrew를 통한 설치

### how to
1. [HomeBrew](http://brew.sh/) 설치
2. mysql 설치 : `$ brew install mysql`
3. mysql 시작 : `$ mysql.server start`
4. root 비밀번호 설정 : `$ mysql_secure_installation`
  * `Would you like to setup VALIDATE PASSWORD plugin?` : 비밀번호 가이드. 복잡한 비밀번호를 사용하도록 제한해주는 플러그인을 사용하려면 `yes`, ~~보안은 무시하고~~ 그냥 쓰던 비밀번호 제한받지 않고 쓰고 싶다면 `no`.
  * `Remove anonymous users?` : 익명사용자 삭제할지/계속 사용할지 여부. `no` 하면 `$ mysql -uroot`가 아니라 `$ mysql`만으로도 접속 가능. `yes`하면 `-u` 옵션 필수.
  * `Disallow root login remotely?` : localhost외에 다른 ip에서 root 아이디로 원격접속 가능하게 할지. `yes`하면 원겹접속 불가.
  * `Remove test database and access to it?` : mysql에 기본적으로 설정된 test 디비 삭제 여부.
  * `Reload privilege tables now?` : 하나라도 권한 변경을 했다면 `yes` 해서 하는 게 정신건강에 좋을 것입니다.
5. charset 설정 : `$ mysql -uroot -p`로 로그인하고, `> status;`로 charset 확인.
  * 기본적으로 charset 4개가 모두 utf8이어서 그대로 두면 됨!
  * 혹시나 latin등 다른 값이 있다면 위의 **4. charset UTF-8로 바꾸기** 따라하기. `my.cnf`가 기본적으로 존재하지 않으므로 `$ vi /etc/my.cnf` 등을 통해 새로 생성.
6. 데몬 실행 `$ brew services start mysql` ([참고](https://robots.thoughtbot.com/starting-and-stopping-background-services-with-homebrew))
  * 로그인시 데몬 자동 실행 (To have launchd start mysql at login): `$ ln -sfv /usr/local/opt/mysql/*.plist ~/Library/LaunchAgents`
7. +, brew로 설치했다면 환경변수 설정은 필요없어요! :)

### versions
brew는 기본적으로 최신 버전(5.7)을 제공하지만, 다른 버전(5.6 등)을 설치하고 싶은 경우 [brew versions](https://github.com/Homebrew/homebrew-versions)를 사용합니다.

1. `$ brew tap homebrew/versions`로 brew versions를 설치 후,
2. `$ brew search mysql`로 검색, 원하는 버전의 formula 이름을 확인합니다. (예: `homebrew/versions/mysql56`)
3. `$ brew install <설치할 formula>`으로 mysql 설치. (예: `$ brew install homebrew/versions/mysql56`)







## Remove MySQL completely

1. Open the Terminal
2. Use `mysqldump` to backup your databases
3. Check for MySQL processes with: `ps -ax | grep mysql`
4. Stop and kill any MySQL processes
5. Analyze MySQL on HomeBrew:

    ```
    brew remove mysql
    brew cleanup
    ```

6. Remove files:

    ```
    sudo rm /usr/local/mysql
    sudo rm -rf /usr/local/var/mysql
    sudo rm -rf /usr/local/mysql*
    sudo rm ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist
    sudo rm -rf /Library/StartupItems/MySQLCOM
    sudo rm -rf /Library/PreferencePanes/My*
    ```

7. Unload previous MySQL Auto-Login:

    ```
    launchctl unload -w ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist
    ```

8. Remove previous MySQL Configuration:

    ```
    subl /etc/hostconfig`
    # Remove the line MYSQLCOM=-YES-
    ```

9. Remove previous MySQL Preferences:

    ```
    rm -rf ~/Library/PreferencePanes/My*
    sudo rm -rf /Library/Receipts/mysql*
    sudo rm -rf /Library/Receipts/MySQL*
    sudo rm -rf /private/var/db/receipts/*mysql*
    ```

10. Restart your computer just to ensure any MySQL processes are killed
11. Try to run mysql, **it shouldn't work**

참고
* [vitorbritto](https://gist.github.com/vitorbritto/0555879fe4414d18569d)
* [helloheesu](https://github.com/helloheesu/SecretlyGreatly/wiki/%EB%A7%A5%EC%97%90%EC%84%9C-mysql-%EC%84%A4%EC%B9%98-%ED%9B%84-%ED%99%98%EA%B2%BD%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0/_edit)
