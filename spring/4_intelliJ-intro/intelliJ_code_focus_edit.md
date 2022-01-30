# 0. 소개
### 프로젝스 생성

1. 의존성 관리
  * 프로젝트는 되도록 `Maven`, `gradle` 로 만드는 것을 추천한다.
  * 라이브러리 사용시 의존성 관리를 편하게 할 수 있다.


2. 프로젝트 생성
  * GroupID : 프로젝트 그룹 ex ) spring
  * Artifact : 그룹의 하위 모듈 ex ) spring-security
  * Version : 버전 관리


# 1. 코드 Edit
### 메인메소드 생성 및 실행
  * 클래스, 디렉토리, 새 파일 생성
    * Mac : `command+ N`
    * 윈도우, 리눅스 : `Alt + Insert`
  * 코드템플릿
    * `psvm` : `public static void main(String[] args)`
    * `sout` : `System.out.println`


  * 현재 포커스 실행환경 Run
    * Mac : `ctrl + shift  + r`
    * 윈도우, 리눅스 : `shift + ctrl + F10`

  * 이전에 실행 Run
    * Mac : `ctrl + r`
    * 윈도우, 리눅스 : `shift + F10`


### 라인 수정하기
  * 라인 복제하기 :  `command + d` (`ctrl+ d`)
    
  * 라인 삭제하기 : `command + 백스페이스` (`ctrl+ y`)
    
  * 문자열 라인 단위 합치기 (String 문자열, join) : `ctrl + shift + j` ( `ctrl+ shift + j`)


  * 라인 단위로 옮기기

  	* 순수 라인 단위로 옮기기 : `option + shift + 상/하 방향키` (`alt+ shift + 상/하 방향키`)
  	* 구문 단위로 옮기기 : `command+ shift + 상/하 방향키` (`ctrl+ shift + 상/하 방향키`)
  
  * Element 단위로 옮기기 : `option + shift + command + 좌/우 방향키` (`alt + shift + command + 좌/우 방향키`)


### 코드 즉시 보기
  * 인자값 즉시 보기 : `command + p` (`ctrl + p`)
  * 코드 구현부 즉시 보기 : `option + space` (`ctrl + shift + I`)
  * Doc 즉시 보기 : `F1` (`ctrl + Q`)  


# 2. 포커스 에디터
### 포커스 에디터
  * 단어별 이동 : `Option + 좌/우 방향키` (`alt + 좌/우 방향키`)
  * 단어별 선택 : `shift + Option + 좌/우 방향키` (`shift  + Alt + 좌/우 방향키`)
  * 라인 첫/끝 이동 : `Fn + 좌/우 방향키` (`Home, End`)
  * 라인 전체 선택 : `Shift + Fn + 좌/우 방향키` (`Shit + Home, End`)
  * Page Up/Down : `Fn + 상/하 방향키` (`PageUp, PageDown`)

### 포커스 특수키
  * 포커스 범위 한단계씩 늘리기 : `option + 상/하 방향키` (`ctrl + w(위) / shift + ctrl + w(아래)`)
      * 괄호가 많은 함수를 중첩해서 쓰거나 파라미터가 여러개 일 때 유용하다.
  * 포커스 뒤로/앞으로 가기 :  `command + [ , ]` (`ctrl + alt + 좌/우 방향키`)
  * 멀티 포커스 : `option + option + 상/하 방향키`  (`ctrl + ctrl + 아래 방향키`)
  * 오류 라인으로 자동 포커스 : `F2` (`F2`)

