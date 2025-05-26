# N+1 문제

## 개념

- N+1 문제는 ORM(Object-Relational Mapping)을 사용할 때 발생하는 성능 이슈
- 하나의 쿼리(1)를 실행한 후 그 결과로 얻은 N개의 레코드 각각에 대해 추가 쿼리를 실행하게 되어 총 N+1번의 쿼리가 실행되는 문제

## 발생 원인

- N+1 문제는 주로 다음과 같은 상황에서 발생:

1. 연관 관계가 있는 엔티티를 조회할 때 (일대다, 다대일 등)
2. 지연 로딩(Lazy Loading)을 사용할 때
3. 연관된 엔티티를 명시적으로 함께 로드하지 않을 때

## 예시 상황

- 예를 들어, 블로그 시스템에서 게시글(Post)과 댓글(Comment)이 일대다 관계를 가진다고 가정

```java
@Entity
@Getter
@Setter
public class Post {
    @Id @GeneratedValue
    private Long id;

    private String title;
    private String content;

    @OneToMany(mappedBy = "post", fetch = FetchType.LAZY)
    private List<Comment> comments = new ArrayList<>();
}

@Entity
@Getter
@Setter
public class Comment {
    @Id @GeneratedValue
    private Long id;

    private String content;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "post_id")
    private Post post;
}
```

## N+1 문제 발생 코드 예시

```java
// PostRepository.java
public interface PostRepository extends JpaRepository<Post, Long> {
    // 기본 메서드만 사용
}

// PostService.java
@Service
public class PostService {

    private final PostRepository postRepository;

    public PostService(PostRepository postRepository) {
        this.postRepository = postRepository;
    }

    public List<Post> getAllPostsWithComments() {
        List<Post> posts = postRepository.findAll(); // 1번의 쿼리 실행

        // N+1 문제 발생 지점
        for (Post post : posts) {
            // 각 게시글마다 댓글을 조회하기 위해 추가 쿼리 실행 (N번)
            List<Comment> comments = post.getComments();
            System.out.println("게시글 ID: " + post.getId() + ", 댓글 수: " + comments.size());
        }

        return posts;
    }
}
```

위 코드에서 `postRepository.findAll()`로 모든 게시글을 조회한 후(1번 쿼리), 각 게시글의 댓글을 조회하기 위해 `post.getComments()`를 호출할 때마다 추가 쿼리가 실행(N번 쿼리). 따라서 총 N+1번의 쿼리가 실행됨.

## 실제 실행되는 SQL 쿼리

```sql
-- 1번 쿼리: 모든 게시글 조회
SELECT * FROM post;

-- N번 쿼리: 각 게시글의 댓글 조회
SELECT * FROM comment WHERE post_id = 1;
SELECT * FROM comment WHERE post_id = 2;
SELECT * FROM comment WHERE post_id = 3;
...
SELECT * FROM comment WHERE post_id = N;
```

## 해결 방법

### 1. 즉시 로딩(Eager Loading) 사용

```java
@OneToMany(mappedBy = "post", fetch = FetchType.EAGER)
private List<Comment> comments = new ArrayList<>();
```

- 즉시 로딩은 엔티티를 조회할 때 연관 엔티티도 함께 조회
- 연관 엔티티가 많은 경우 성능 저하 발생 가능
- 연관 엔티티를 항상 사용하는 경우에만 적합

하지만 이 방법은 항상 연관 엔티티를 함께 로드하므로 필요하지 않은 경우에도 데이터를 가져오게 되어 비효율적일 수 있음.

### 2. 조인 페치(Join Fetch) 사용

```java
// PostRepository.java
public interface PostRepository extends JpaRepository<Post, Long> {
    @Query("SELECT p FROM Post p JOIN FETCH p.comments")
    List<Post> findAllWithComments();
}

// PostService.java
public List<Post> getAllPostsWithComments() {
    return postRepository.findAllWithComments(); // 한 번의 쿼리로 게시글과 댓글을 함께 조회
}
```

- JPQL의 JOIN FETCH 구문을 사용해 연관 엔티티를 한 번에 조회
- 한 번의 쿼리로 모든 데이터를 가져와 N+1 문제 해결
- 다대다 관계에서는 중복 데이터가 발생할 수 있어 distinct 키워드 사용 필요

### 3. EntityGraph 사용

```java
// PostRepository.java
public interface PostRepository extends JpaRepository<Post, Long> {
    @EntityGraph(attributePaths = {"comments"})
    List<Post> findAll();
}
```

- JPQL 작성 없이 연관 엔티티 조회 가능
- 조인 페치보다 사용하기 쉽고 유연한 방법
- JPA에서 제공하는 Fetch Join 기능

### 4. 배치 사이즈(Batch Size) 설정

```java
@OneToMany(mappedBy = "post", fetch = FetchType.LAZY)
@BatchSize(size = 100) // 한 번에 100개의 연관 엔티티를 로드
private List<Comment> comments = new ArrayList<>();
```

또는 `application.yml`에 글로벌 설정:

```yaml
# application.yml
spring:
  jpa:
    properties:
      hibernate:
        default_batch_fetch_size: 100
```

- 지정한 개수만큼 연관 엔티티를 한 번에 조회하는 방식
- N+1 문제를 N/BatchSize+1 문제로 줄이는 효과
- 지연 로딩을 유지하면서도 성능 향상 가능

## 결론

N+1 문제는 ORM을 사용할 때 흔히 발생하는 성능 이슈. 이 문제 해결을 위해 조인 페치, EntityGraph, 배치 사이즈 설정 등의 방법을 적절히 사용해야 함. 또한, 성능 테스트와 모니터링을 통해 실제 애플리케이션에서 어떤 방법이 가장 효과적인지 확인하는 것이 중요.
