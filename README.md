# Quick jump
- __Snippets__
  - [Read input (loop)](https://github.com/jbilee/woowa-test-notes#read-input-loop)
  - [Output view](https://github.com/jbilee/woowa-test-notes#output-view)
  - [Self-validating object](https://github.com/jbilee/woowa-test-notes#self-validating-object)
  - [Split util](https://github.com/jbilee/woowa-test-notes#split-util)
  - [Multi-value enum](https://github.com/jbilee/woowa-test-notes#multi-value-enum)
  - [Error message enum](https://github.com/jbilee/woowa-test-notes#error-message-enum)
  - [Regex-based validator](https://github.com/jbilee/woowa-test-notes#regex-based-validator)
  - [File reader util](https://github.com/jbilee/woowa-test-notes#file-reader-util)
  - [MissionUtils import](https://github.com/jbilee/woowa-test-notes#missionutils-import)
  - [README](https://github.com/jbilee/woowa-test-notes#readme)
- __Java notes__
  - [List](https://github.com/jbilee/woowa-test-notes#list)
  - [Map](https://github.com/jbilee/woowa-test-notes#map)
  - [Stream](https://github.com/jbilee/woowa-test-notes#stream)
  - [LocalDate](https://github.com/jbilee/woowa-test-notes#localdate)
  - [Text formatting](https://github.com/jbilee/woowa-test-notes#text-formatting)
  - [Testing](https://github.com/jbilee/woowa-test-notes#testing)
- __[File directory](https://github.com/jbilee/woowa-test-notes#file-directory)__
- __[Working order](https://github.com/jbilee/woowa-test-notes#working-order)__
- __[Priorities](https://github.com/jbilee/woowa-test-notes#priorities)__

# Snippets

## Read input (loop)
```java
// Prompt enum
public enum InputPrompts {
    A_PROMPT(""),
    B_PROMPT("");

    private final String text;

    InputPrompts(String text) {
        this.text = text;
    }

    public String getText() {
        return text;
    }
}

// View
public class InputView {
    public String read() {
        System.out.println(InputPrompts.KEY.getPrompt());
        return Console.readLine().strip();
    }
}

// Loop
private static String getWeekdayNames(InputView inputView, Validator validator) {
    try {
        String nameInput = inputView.readWeekdayNames();
        validator.checkNames(nameInput);
        return nameInput;
    } catch (IllegalArgumentException e) {
        System.out.println(e.getMessage());
        return getWeekdayNames(inputView, validator);
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

## Self-validating object
```java
public class Lotto {
    private final List<Integer> numbers;

    public Lotto(List<Integer> numbers) {
        validate(numbers);
        this.numbers = numbers;
    }

    private void validate(List<Integer> numbers) {
        if (numbers.size() != LottoValues.LOTTO_SIZE.getValue()) {
            throw new IllegalArgumentException(ErrorMessages.INCORRECT_LOTTO_SIZE.getMessage());
        }
        if (numbers.stream().distinct().count() != LottoValues.LOTTO_SIZE.getValue()) {
            throw new IllegalArgumentException(ErrorMessages.LOTTO_NUMBER_CONTAINS_DUPLICATE.getMessage());
        }
        if (numbers.stream().anyMatch(number -> number > LottoValues.MAX_NUMBER.getValue())) {
            throw new IllegalArgumentException(ErrorMessages.LOTTO_NUMBER_NOT_WITHIN_RANGE.getMessage());
        }
        if (numbers.stream().anyMatch(number -> number < LottoValues.MIN_NUMBER.getValue())) {
            throw new IllegalArgumentException(ErrorMessages.LOTTO_NUMBER_NOT_WITHIN_RANGE.getMessage());
        }
    }
}
```

## Split util
```java
public class Split {
    private final static String DELIMITER = ",";

    public static List<String> getAsList(String string, boolean checkUniqueness) {
        List<String> result = List.of(string.split(DELIMITER));
        if (checkUniqueness) {
            int count = result.size();
            int uniqueCount = (int) result.stream().distinct().count();
            if (count != uniqueCount) {
                throw new IllegalArgumentException(ErrorMessages.INVALID.getMessage());
            }
        }
        return result;
    }
}
```

## Multi-value enum
```java
// Enum
public enum MenuItems {
    JP("일식", List.of("규동, 우동, 미소시루, 스시, 가츠동, 오니기리, 하이라이스, 라멘, 오코노미야끼".split(", "))),
    NONE("없음", Collections.EMPTY_LIST);

    private final String category;
    private final List<String> items;

    MenuItems(String category, List<String> items) {
        this.category = category;
        this.items = items;
    }

    public static MenuItems findByItem(String name) {
        return Arrays.stream(MenuItems.values()) // OR Stream.of(MenuItems.values())
                .filter(category -> category.hasItem(name))
                .findAny()
                .orElse(NONE);
                // OR throw right away
                .orElseThrow(() -> new IllegalArgumentException(""));
    }

    public boolean hasItem(String name) {
        return items.stream().anyMatch(item -> item.equals(name));
    }

    public String getCategory() {
        return category;
    }
}

// Check if something in enum exists
MenuItems result = MenuItems.findByItem(name);
if (result.getCategory().equals("없음")) {
    throw new IllegalArgumentException(ErrorMessages.INVALID.getMessage());
}

// Print in order
public void printLottoResults(LottoResults lottoResults) {
    RewardTable[] rewardTableKeys = RewardTable.values();
    for (RewardTable key : rewardTableKeys) {
        int value = lottoResults.getMatchValue(key);
        System.out.println(key.getLabel() + value + ResultText.COUNT_SUFFIX.getText());
    }
}
```

## Error message enum
```java
public enum ErrorMessages {
    INVALID("[ERROR] 잘못된 입력입니다. 다시 입력해 주세요.");

    private final String message;

    ErrorMessages(String message) {
        this.message = message;
    }

    public String getMessage() {
        return message;
    }
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
        return content.stream().skip(HEADERS); // If needed, select up to how many rows of data to skip
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

## README
```
# 구현할 기능 목록

### 입력 및 입력값 검증 기능
- [ ] 입력 받는다
  - [ ] 지 검증한다
  - [ ] 지 검증한다
- [ ] 입력값이 유효하지 않을 경우 오류 메세지를 출력하고 입력을 다시 받는다

### 관련 기능
- [ ] 

### 출력 기능
- [ ] 출력한다
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

Comparing two Lists to check if they both contain the same objects
```java
List<String> arr1 = new ArrayList<>(List.of("a", "b", "c")); // Need to create new ArrayList<>() so that it can be mutated
List<String> arr2 = List.of("a", "c", "b");
List<String> arr3 = List.of("a", "b", "e");

System.out.println(arr1.retainAll(arr2)); // false
System.out.println(arr1.retainAll(arr3)); // true
System.out.println(arr1.toString()); // [a, b]
```

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

Convert list items to primitive integers for mathematical operations
```java
// Where `items` is a Map of Integer objects
items.values().stream().mapToInt(Integer::intValue).sum();
```

Count the number of unique values in a List<T>
```java
numbers.stream().distinct().count();
```

Combining lists
```java
Stream.concat(list1.stream(), list2.stream())
      .collect(Collectors.toList());
```

## LocalDate
Create a LocalDate
```java
// Directly
LocalDate myDate = LocalDate.of(2024, 12, 31);
```
```java
// From a string
LocalDate myDate = LocalDate.parse("2024-12-31");
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

Testing output
```java
import static camp.nextstep.edu.missionutils.test.Assertions.assertSimpleTest;
import static org.assertj.core.api.Assertions.assertThat;

import camp.nextstep.edu.missionutils.test.NsTest;
import org.junit.jupiter.api.Test;

class ApplicationTest extends NsTest {
    private static final String LINE_SEPARATOR = System.lineSeparator();

    @Test
    void 예외_테스트() {
        assertSimpleTest(() -> {
            run("0,일",
                    "4,토",
                    "허브,쥬니,말랑,라온,헤나,우코,에단,수달,파워,히이로,마코,슬링키,모디,연어,깃짱,리오,고니,박스터,달리,조이,노아이즈,도이,도치,홍고,스캇,폴로,해시,로지,첵스,아이크,우가,푸만능,애쉬,로이스,오션",
                    "오션,로이스,애쉬,푸만능,우가,아이크,첵스,로지,해시,폴로,스캇,홍고,도치,도이,노아이즈,조이,달리,박스터,고니,리오,깃짱,연어,모디,슬링키,마코,히이로,파워,수달,에단,우코,헤나,라온,말랑,쥬니,허브"
            );
            assertThat(output()).contains(
                    "[ERROR]",
                    "4월 1일 토 오션" + LINE_SEPARATOR
            );
        });
    }

    @Override
    protected void runMain() {
        Application.main(new String[]{});
    }
}
```
Run Gradle test with `gradlew.bat clean test` or `./gradlew.bat clean test`

- Possible tests
  - Return value in an enum
  - Return value of a simple calculation
  - Testing validation logic in a self-validating object, assert throws

# File directory
![image](https://github.com/user-attachments/assets/1e77df19-d61a-40f4-8ccf-6803b6fc2131)

# Working order
1. Set build tool to IntelliJ
2. Write quick README based on reqs, make mental model of what I would need and try to digest reqs as fast as possible
3. Draw diagrams; determine objects needed and draw out relationships between them (also check default tests, and update README if needed)
4. Create `ui` and `ui.constants` packages: InputView, OutputView, ErrorMessages, InputPrompts
5. Create `domains` package: Sub-domains based on objects needed (validate here directly in smaller objects)
6. Create `helpers` package: Project-specific data manipulation/formatting
7. (Optional) Create `utils` package: FileReader, etc.

***Spend little time on validation = ignore edge cases for inputs

# Priorities
(If there isn't enough time left to do anything time-consuming like writing intricate tests, refactoring main logic, etc.)
1. Split functions
2. Write simple tests
3. Cut down on indentation
4. Think of more intuitive names for functions and variables
5. Spend ~10 mins on 소감문

https://www.protectedtext.com/dydk1215
