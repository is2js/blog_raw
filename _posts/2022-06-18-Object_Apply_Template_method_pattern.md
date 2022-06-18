---
toc: true
layout: post
title: Object)Template method pattern
description: 구현체들만 있는 상태에서 공통+개별로직을 템플릿메소드 패턴으로 추출

categories: [java, 우테코, object, templatemethod, pattern]
image: "images/posts/java.png"
---

### 구현체에서 템플릿메소드 패턴으로 추상클래스 만들기

- 카테고리로만 적용했던 마커인터페이스를 삭제한 상태이다
    - 공통로직이 발견되었고, 개별구현 로직을 포함한 상태이다.

1. 구현체에서 **미리 템플릿메소드패턴으로 구성**을 해놓아야한다.

    1. 한번에 일 처리해줄(인터페이스 or 추상클래스) && 자식은 물려받아 안보이면서 구현안해도 되는 `public (템플릿)메소드 1개`가 **전체로직의 내부에서 개별구현로직인 `private 훅메서드들(step메서드들을) 감싸고 있는 형태`**여야한다.
    2. **구상체별 개별구현 될 로직**(step메서드들, proteced abstact로 올라갈)들은 **public 템플릿메소드 `안`에서 `순서대로 private 훅메서드로 추출된 상태`**여야한다
        - **`private으로 추출된 메서드`가 있어야, @Override로 superclass 추출할 때, `abstract`를 선택하고, 그 선택이 일반부모class 추출 -> abstact class로 추출을 만든다.**
        - **또한 그 훅메서드들(템플릿메소드안에 step들을 가지며 개별구현 될)은 `메서드명이 통일`되어있어야한다.**

    

    

2. 현재 코드

    - BackEnd class와 FrontEnd class의 공통로직은 public 템플릿메소드 1개로, 개별구현 로직들은 private메소드로 만들어, `Programmer`라는 추상클래스로 추상화하고 싶다.

        - 백엔드 클래스

        ```java
        package developer.domain.progammer;
        
        public class BackEnd {
        
            private Server server;
            private Language language;
        
            public Program makeProgram(final Paper paper) {
                if (paper instanceof ServerClient) {
                    final ServerClient pa = (ServerClient) paper;
                    server = pa.getServer();
                    language = pa.getBackEndLanguage();
                }
        
                return makeBackEndProgram("백엔드 프로그램");
            }
        
            private Program makeBackEndProgram(final String name) {
                return new Program(name);
            }
        }
        
        ```

        - 프론트엔드 클래스

        ```java
        package developer.domain.progammer;
        
        public class FrontEnd {
        
            private Language language;
            private Library library;
        
            public Program makeProgram(final Paper paper) {
                if (paper instanceof Client) {
                    final Client pb = (Client) paper;
                    language = pb.getLanguage();
                    library = pb.getLibrary();
                }
        
                return makeFrontEndProgram("프론트 프로그램");
            }
        
            private Program makeFrontEndProgram(final String name) {
                return new Program(name);
            }
        }
        
        ```

        

3. 구상체들을 템플릿형태로 **변경해야할 부분**

    - 공통로직은 public 템플릿메소드 `1개`로 잘 나타나있다.

    - **개별 구현로직은**

        - 서로 다르게 구현되는 `if  ~ return`부분이 **private 훅메서드(step메서드들)로 추출이 안되어있는 상태**다.

            ```java
            // BackEnd.java
            if (paper instanceof ServerClient) {
                final ServerClient pa = (ServerClient) paper;
                server = pa.getServer();
                language = pa.getBackEndLanguage();
            }
            
            // FrontEnd.java
            if (paper instanceof Client) {
                final Client pb = (Client) paper;
                language = pb.getLanguage();
                library = pb.getLibrary();
            }
            ```

            

        - 이미 추출된 훅메서드가 **명칭이 통일이 안되어있는 상태**이다.

            ```java
            // BackEnd.java
            return makeBackEndProgram("백엔드 프로그램");
            
            // FrontEnd.java
            return makeFrontEndProgram("프론트 프로그램");
            ```

            

    

4. 변경해보기

    1. 이미 public 템플릿메소드가 존재하는 상황이다.(**전체로직 & 공통코드를** 담는 public 메서드)

    2. 개별구현로직도 순서대로 이름이 통일된 **private 훅메서드로 추출**한다.

        ![60dde2e3-a345-4be7-9393-b6c84b359bfb](https://raw.githubusercontent.com/is2js/screenshots/main/60dde2e3-a345-4be7-9393-b6c84b359bfb.gif)

    3. **구현체별로 이름이 다른 훅메서드들의 이름을 통일한다.**

        - 추상클래스를 추출할 클래스에서만, 메서드명을 추상화해서 통일하고
        - 나머지 구현체들은 그대로 두자. -> **이미 동일한 메서드를 가지고 있으면, `자동implements`가 인식안되서 직접 작성해줘야한다.**
            - 아래 영상에서는 FrontEnd의 메서드명도 수정하는데 그대로 두던지, `수정하고 주석처리`해놓자.

        ![71015c26-1fa5-4b14-8cf4-f2eacc1ca8c0](https://raw.githubusercontent.com/is2js/screenshots/main/71015c26-1fa5-4b14-8cf4-f2eacc1ca8c0.gif)

    4. **1개의 구현체**에서 템플릿메소드패턴을 적용한 추상클래스를 추출한다.

        - public 템플릿메소드를 추출하려고 하면, **private메서드들이 빨간색 뜨는데, 이놈들을 abstract로 데려가면, 구상체별 개별구현 훅메서드가 된다.** 

        ![59eeca1f-d228-4c1d-aacd-eddc152b6f2d](https://raw.githubusercontent.com/is2js/screenshots/main/59eeca1f-d228-4c1d-aacd-eddc152b6f2d.gif)

    5. **나머지 구현체들**은, 추상클래스를 상속한 뒤

        1. 공통로직을 담은 **public템플릿메소드는 제거하여 추상클래스로부터 물려받아 쓴다.**
        2. 개별구현로직의 **private훅메서드들은 주석처리해놓고 -> implements해서 구현한 뒤 -> 개별구현로직을 복붙**하자.
            - **메서드명이 추상메서드와 동일하면, implements에 안뜬다.**
            - 메서드명이 추상메서드와 동일하게 그대로 두면, 접근제어자에만 빨간색떠서 직접 수정해줘야한다.

        ![e8835bc1-377f-405a-a924-8b91297b6c17](https://raw.githubusercontent.com/is2js/screenshots/main/e8835bc1-377f-405a-a924-8b91297b6c17.gif)

        

