# Quick jump
- __Snippets__
  - [MissionUtils import](https://github.com/jbilee/woowa-test-notes#missionutils-import)
  - [Read input](https://github.com/jbilee/woowa-test-notes#read-input)
  - [Output view](https://github.com/jbilee/woowa-test-notes#output-view)
  - [Input loop](https://github.com/jbilee/woowa-test-notes#input-loop)
  - [Regex-based validator](https://github.com/jbilee/woowa-test-notes#regex-based-validator)
  - [Single-value enum](https://github.com/jbilee/woowa-test-notes#single-value-enum)
  - [Multi-value enum](https://github.com/jbilee/woowa-test-notes#multiple-value-enum)
  - [Error message enum](https://github.com/jbilee/woowa-test-notes#error-message-enum)
  - [File reader util](https://github.com/jbilee/woowa-test-notes#file-reader-util)

- __How to use Java__
  - [List](https://github.com/jbilee/woowa-test-notes#list)
  - [Date object](https://github.com/jbilee/woowa-test-notes#date-object)
  - [Stream](https://github.com/jbilee/woowa-test-notes#stream)
  - [Text formatting](https://github.com/jbilee/woowa-test-notes#text-formatting)

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
import java.util.stream.Collectors;

public class FileReader {
    public String readContent(String path) {
        if (path.isEmpty()) {
            throw new IllegalStateException(ErrorMessages.NO_DATA.getMessage());
        }
        return getString(path);
    }

    private String getString(String path) {
        try {
            InputStream inputStream = Application.class.getResourceAsStream(path);
            BufferedReader reader = new BufferedReader(new InputStreamReader(inputStream));
            return reader.lines().collect(Collectors.joining("\n"));
        } catch (NullPointerException e) {
            throw new IllegalStateException(ErrorMessages.NO_DATA.getMessage());
        }
    }
}
```
```java
import java.util.Arrays;
import java.util.stream.Stream;

public class DataHandler {
    private static final String DELIMITER = "\\n";
    private static final int HEADERS = 1;

    public Stream<String> readData(String path) {
        FileReader reader = new FileReader();
        String content = reader.readContent(path);
        return Arrays.stream(content.split(DELIMITER)).skip(HEADERS);
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
List<String> numbers = List.of(lottoNumbers.split(LOTTO_SPLIT_DELIMITER));
```
## Date object
LocalDate object 
## Stream
List-stream-list conversion
## Text formatting
```java
String.format("%,d)
```

# File directory
![image](https://github.com/user-attachments/assets/1e77df19-d61a-40f4-8ccf-6803b6fc2131)
