# System.in.read
```java
public class Main {
    public static String getStringFromKeyboard() {
        // 키보드에서 입력 받을 캐릭터 배열 생성
        char[] characters = new char[1024];
        try {
            // 캐릭터 배열의 크기만큼 키보드에서 입력 받음
            for (int index = 0; index < characters.length; index++) {
                // 키보드를 통해 입력 받은 아스키코드값
                int asciiCode = System.in.read();
                // 아스키코드값을 캐릭터로 변경
                char character = (char) asciiCode;
                if (asciiCode == 10) {
                    // 엔터키를 입력 받았다면, for문 종료
                    break;
                } else {
                    // 엔터키가 아닌 경우, 캐릭터 배열에 캐릭터 넣기
                    characters[index] = character;
                }
            }
        } catch (IOException ioException) {
            // System.in.read에서 필요한 IOException
            ioException.printStackTrace();
        }
        // 캐릭터 배열을 문자열로 만들기
        String string = new String(characters);
        // 공백을 제거한 문자열을 return
        return string.trim();
    }
    public static void main(String[] args) {
        System.out.println(getStringFromKeyboard());
    }
}
```
