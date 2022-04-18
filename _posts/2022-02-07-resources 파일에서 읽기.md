---
toc: true
layout: post
title: resources 속 파일 읽기
description: pair-matching 구현시 크루들 이름을 md파일에서 읽어오기

categories: [java, resources, pair-matching, 우테코]
image: "images/posts/java.png"
---

## 외부파일 읽기



### 01 src>main>resources 안에 파일을 넣는다.

![image-20220414142954786](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220414142954786.png)





### 02 카테고리(분기)가 있는 경우, enum에 외부문자열로서 파일경로 매핑

#### 파일경로는 "src/main/resources/ ~ "로서 src부터 시작한다

![image-20220414143503419](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220414143503419.png)

```java
public enum Course {
    BACKEND("백엔드", "src/main/resources/backend-crew.md"),
    FRONTEND("프론트엔드", "src/main/resources/frontend-crew.md");

    private String name;
    private final String resourcePath;

    Course(String name, final String resourcePath) {
        this.name = name;
        this.resourcePath = resourcePath;
    }
```





### 03 new File(경로) -> new Scanner( file ) -> if .hasNextLine() -> .nextLine()

#### 한줄 씩 읽어서 -> 가변 `List<String>`에  `한줄씩 add`하는 것이 특징

- utils > `FileReaderUtil`

    ```java
    public class FileReaderUtil {
        public static List<String> read(final String pathname) {
            final File file = new File(pathname);
            final List<String> crews = new ArrayList<>();
            try {
                final Scanner scanner = new Scanner(file);
                while (scanner.hasNextLine()) {
                    crews.add(scanner.nextLine());
                }
            } catch (FileNotFoundException e) {
                System.out.println("[ERROR] " + e.getMessage());
            }
    
            return crews;
        }
    }
    ```

    

    ```java
    public Crews generateShuffledCrews(final Course course) {
        final List<String> crewNames = FileReaderUtil.read(course.getResourcePath());
        final List<String> shuffledCrew = Randoms.shuffle(crewNames);
        return Crews.from(course, shuffledCrew);
    }
    
    ```

    