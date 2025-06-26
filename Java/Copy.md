# 얕은 복사(Shallow Copy)와 깊은 복사(Deep Copy)

백엔드 개발에서 객체를 복사할 때 두 가지 중요한 개념이 있습니다: **얕은 복사**와 **깊은 복사**입니다.  
다음은 자바 예제를 통해 이 개념을 설명합니다.

---

## 클래스 정의

```java
class Book {

    private String name; // 책 이름
    private Author author; // 저자

    public Book(String name, Author author) {
        this.name = name;
        this.author = author;
    }

    // 얕은 복사: Author 객체는 새로 만들지 않고 참조만 복사
    public Book shallowCopy() {
        return new Book(this.name, this.author);
    }

    // 깊은 복사: Author 객체도 새로 만들어 복사
    public Book deepCopy() {
        Author copiedAuthor = new Author(this.author.getName());
        return new Book(this.name, copiedAuthor);
    }

    public void changeAuthor(String name) {
        author.setName(name);
    }

    @Override
    public String toString() {
        return "Book name : " + name + ", " + author;
    }

    static class Author {

        private String name; // 저자 이름

        public Author(String name) {
            this.name = name;
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        @Override
        public String toString() {
            return "Author : " + name;
        }
    }

    public static void main(String[] args) {
        Author author1 = new Author("조슈아 블로크");
        Book book1 = new Book("이펙티브 자바", author1);

        // 얕은 복사 후 저자 이름 변경
        Book shallowCopyBook = book1.shallowCopy();
        shallowCopyBook.changeAuthor("Joshua Bloch");

        System.out.println("After shallow copy and change:");
        System.out.println("Original book1: " + book1);
        System.out.println("Shallow copied book: " + shallowCopyBook);

        Author author2 = new Author("마틴 파울러");
        Book book2 = new Book("리팩터링", author2);

        // 깊은 복사 후 저자 이름 변경
        Book deepCopyBook = book2.deepCopy();
        deepCopyBook.changeAuthor("Martin Fowler");

        System.out.println("\nAfter deep copy and change:");
        System.out.println("Original book2: " + book2);
        System.out.println("Deep copied book: " + deepCopyBook);
    }
}
```

### 얕은 복사 (Shallow Copy)

- shallowCopy() 메서드는 새로운 Book 객체를 생성하지만, 내부의 Author 객체는 같은 인스턴스를 참조합니다.
- 따라서 복사본에서 저자 이름을 바꾸면, 원본 Book의 저자 이름도 같이 바뀝니다.
- 이유: Book 객체는 새로 생성했지만 Author 객체는 공유하기 때문입니다.

### 깊은 복사 (Deep Copy)

- deepCopy() 메서드는 Book 객체뿐 아니라 Author 객체도 새로운 인스턴스로 생성합니다.
- 복사본의 저자 이름을 변경해도 원본의 저자 이름에는 영향을 주지 않습니다.
- 이유: 서로 다른 Author 객체를 참조하기 때문입니다.

