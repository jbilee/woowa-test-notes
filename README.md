# Quick jump
- __Snippets__
  - [MissionUtils import](https://github.com/jbilee/woowa-test-notes#missionutils-import)
  - [Read input](https://github.com/jbilee/woowa-test-notes#read-input)
  - [Output view](https://github.com/jbilee/woowa-test-notes#output-view)
  - [Input loop](https://github.com/jbilee/woowa-test-notes#input-loop)
  - [Regex-based validator](https://github.com/jbilee/woowa-test-notes#regex-based-validator)
  - [Single-value enum](https://github.com/jbilee/woowa-test-notes#single-value-enum)
  - [Multi-value enum](https://github.com/jbilee/woowa-test-notes#multi-value-enum)
  - [Error message enum](https://github.com/jbilee/woowa-test-notes#error-message-enum)
  - [File reader util](https://github.com/jbilee/woowa-test-notes#file-reader-util)

- __How to use Java__
  - [List](https://github.com/jbilee/woowa-test-notes#list)
  - [Map](https://github.com/jbilee/woowa-test-notes#map)
  - [Stream](https://github.com/jbilee/woowa-test-notes#stream)
  - [LocalDate](https://github.com/jbilee/woowa-test-notes#localdate)
  - [Text formatting](https://github.com/jbilee/woowa-test-notes#text-formatting)
  - [Testing](https://github.com/jbilee/woowa-test-notes#testing)

- __[File directory](https://github.com/jbilee/woowa-test-notes#file-directory)__

# Snippets

## MissionUtils import
```java
import camp.nextstep.edu.missionutils.Console;
```
```java
import camp.nextstep.edu.missionutils.Randoms;
```
```java
import camp.nextstep.edu.missionutils.DateTimes;
```

## Read input
```java
public class InputView {
    public String readOrder() {
        System.out.println(InputPrompts.PURCHASE.getPrompt());
        return Console.readLine().strip();
    }
}
```

## Output view
```java
public class OutputView {
    public void printPurchasedItemLine(String name, int quantity, int cost) {
        System.out.println(name + "\t\t\t" + quantity + "\t\t\t" + String.format("%,d", cost));
    }

    public void printInventory(List<Product> products) {
        System.out.println(InventoryUi.GREETING.getText());
        products.forEach(product -> System.out.println(product.getPrintableString()));
    }
}
```

## Input loop
```java
public void handleOrder() {
    while (true) {
        try {
            saveOrderToCart();
            break;
        } catch (IllegalArgumentException e) {
            System.out.println(e.getMessage());
        }
    }
}

public void saveOrderToCart() {
    String order = inputView.readOrder();
    // Other logic
    // Throw error here for `handleOrder` to catch and display error message
}
```
```java
public boolean handleContinue() {
    while (true) {
        try {
            return getContinueSelection();
        } catch (IllegalArgumentException e) {
            System.out.println(e.getMessage());
        }
    }
}

public boolean getContinueSelection() {
    SelectionValidator validator = new SelectionValidator();
    String input = inputView.readContinueSelection();
    validator.validate(input);
    return input.equalsIgnoreCase("Y");
}
```

## Regex-based validator
```java
public class SelectionValidator {
    private static final String selectionRegex = "[YyNn]";

    public void validate(String input) {
        Pattern pattern = Pattern.compile(selectionRegex);
        Matcher matcher = pattern.matcher(input);
        if (!matcher.find()) {
            throw new IllegalArgumentException(ErrorMessages.INVALID.getMessage());
        }
    }
}
```

## Single-value enum
```java
public enum InventoryUi {
    NAME("text");

    private final String text;

    InventoryUi(String text) {
        this.text = text;
    }

    public String getText() {
        return text;
    }
}
```

## Multi-value enum
```java
public enum RewardTable {
    NAME(0, 0, "text");

    private final int matches;
    private final int rewardAmount;
    private final String label;

    RewardTable(int matches, int rewardAmount, String label) {
        this.matches = matches;
        this.rewardAmount = rewardAmount;
        this.label = label;
    }

    public int getMatches() {
        return matches;
    }

    public int getRewardAmount() {
        return rewardAmount;
    }

    public String getLabel() {
        return label;
    }
}
```

## Error message enum
```java
public enum ErrorMessages {
    INVALID("[ERROR] 잘못된 입력입니다. 다시 입력해 주세요."),
    NO_DATA("[ERROR] 데이터 파일을 찾을 수 없어 프로그램을 종료합니다. resources 파일 경로를 확인해 주세요."),
    NO_PRODUCT("[ERROR] 존재하지 않는 상품입니다. 다시 입력해 주세요."),
    NO_STOCK("[ERROR] 재고 수량을 초과하여 구매할 수 없습니다. 다시 입력해 주세요."),
    WRONG_FORMAT("[ERROR] 올바르지 않은 형식으로 입력했습니다. 다시 입력해 주세요.");

    private final String message;

    ErrorMessages(String message) {
        this.message = message;
    }

    public String getMessage() {
        return message;
    }
}
```

## File reader util
```java
import java.io.BufferedReader;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.util.List;

public class FileReader {
    public List<String> readContent(String path) {
        if (path.isEmpty()) {
            throw new IllegalStateException(ErrorMessages.NO_DATA.getMessage());
        }
        return getStrings(path);
    }

    private List<String> getStrings(String path) {
        try {
            InputStream inputStream = Application.class.getResourceAsStream(path);
            BufferedReader reader = new BufferedReader(new InputStreamReader(inputStream));
            return reader.lines().toList();
        } catch (NullPointerException e) {
            throw new IllegalStateException(ErrorMessages.NO_DATA.getMessage());
        }
    }
}
```
```java
import java.util.List;
import java.util.stream.Stream;

public class DataHandler {
    private static final int HEADERS = 1;

    public Stream<String> readData(String path) {
        FileReader reader = new FileReader();
        List<String> content = reader.readContent(path);
        return content.stream().skip(HEADERS); // Select up to how many rows of data to skip
    }
}
```
```java
import store.helpers.DataHandler;
import store.ui.InputView;

import java.util.stream.Stream;

public class ApplicationConfig {
    private Inventory setupInventory() {
        DataHandler dataHandler = new DataHandler();
        Stream<String> data = dataHandler.readData("/products.md");
        return new Inventory(data);
    }
}
```

# Java notes
## List
Create a list
```java
List<String> letters = List.of("a", "b"); // Or pass in regular Array []
```
```java
private List<String> itemNames = new ArrayList<>(); // Declare a mutable empty List
private List<String> itemNames = new ArrayList<>(List.of("name")); // Declare a mutable filled list
```
If you create a List using `List.of()`, you're creating an __immutable List__--you can't add items to it later using `.add()`

## Map
Create a map
```java
private Map<String, List<String>> eventItems = new HashMap<>(); // Declare an empty Map
```
```java
private Map<String, Integer> test = Map.of("key1", 1, "key2", 2, "key3", 3);
```
Like Lists, if you create a Map using `Map.of()`, you're creating an __immutable Map__.

## Stream
Filter
```java
List<Integer> matchingNumbers = winningNumbers.stream().filter(number -> this.numbers.contains(number)).toList();
```

Count the number of unique values in a List<T>
```java
numbers.stream().distinct().count();
```

## LocalDate
Create a LocalDate
```java
// Directly
LocalDate myDate = LocalDate.of(2024, 12, 31);
```
```java
// From a string
String dateString = "2024-12-31";
LocalDate myDate = LocalDate.parse(dateString);
```

Get the date's corresponding week of month
```java
LocalDate myDate = LocalDate.of(2024, 12, 11);
int weekOfMonth = myDate.get(WeekFields.of(Locale.getDefault()).weekOfMonth());
System.out.println(weekOfMonth); // 2
```

## Text formatting
Turn integer into string with commas
```java
String.format("%,d", value_to_format);
```
```java
// Where `moneys` is a List<String>
moneys.stream().mapToInt(Integer::parseInt).forEach(money -> {
    System.out.println(String.format("%,d", money));
});

// Where `moneys` is a List<Integer>
moneys.stream().mapToInt(Integer::intValue).forEach(money -> {
    System.out.println(String.format("%,d", money));
});
```

## Testing
Simple test
```java
class InventoryTest {
    @DisplayName("존재하지 않는 상품을 조회하려고 하면 예외가 발생한다.")
    @Test
    void 상품_조회_예외_테스트() {
        Stream<String> data = Stream.of("탄산수,1200,5,탄산2+1");
        Inventory inventory = new Inventory(data);
        String itemName = "편의점";
        int itemQuantity = 1;

        assertThrows(IllegalArgumentException.class, () -> inventory.findProduct(itemName, itemQuantity));
    }
}
```

Repeating same test with different parameters (ValueSource and CsvSource)
```java
@ParameterizedTest
@ValueSource(strings = {"a", "1", "", " "})
void 선택지_입력_예외_테스트(String input) {
    SelectionValidator selectionValidator = new SelectionValidator();
    assertThrows(IllegalArgumentException.class, () -> selectionValidator.validate(input));
}
```
```java
@ParameterizedTest
@CsvSource({"3,true", "5,false", "6,true", "10,false"})
void 테스트(int someValue, boolean expectedValue) {
    // ...
    assertEquals(expectedValue, someMethod(someValue));
}
```

Using MethodSource (when CsvSource can't do the job due to commas being reserved in CsvSource arguments, or when complex objects need to be passed as args)
```java
class EventTest {
    @ParameterizedTest
    @MethodSource("divisorTestArgs")
    void 프로모션_혜택_갯수_반환_기능_테스트(String data, int expectedValue) {
        Event event = new Event(data);
        assertEquals(expectedValue, event.getEligibilityDivisor());
    }

    static Stream<Arguments> divisorTestArgs() {
        return Stream.of(
                Arguments.of("탄산2+1,2,1,2024-01-01,2024-12-31", 3),
                Arguments.of("반짝할인,1,1,2024-11-01,2024-11-30", 2)
        );
    }
}
```
Run Gradle test with `gradlew.bat clean test` or `./gradlew.bat clean test`

# File directory
![image](https://github.com/user-attachments/assets/1e77df19-d61a-40f4-8ccf-6803b6fc2131)
