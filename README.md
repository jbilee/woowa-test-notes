# Quick jump
- __Snippets__
  - [MissionUtils import](https://github.com/jbilee/woowa-test-notes#missionutils-import)
  - [Read input](https://github.com/jbilee/woowa-test-notes#read-input)
  - [Output view](https://github.com/jbilee/woowa-test-notes#output-view)
  - [Validator](https://github.com/jbilee/woowa-test-notes/edit/main/README.md#validator)
  - [Single-value enum](https://github.com/jbilee/woowa-test-notes/edit/main/README.md#single-value-enum)
  - [Multi-value enum](https://github.com/jbilee/woowa-test-notes/edit/main/README.md#multiple-value-enum)
  - [Error message enum](https://github.com/jbilee/woowa-test-notes/edit/main/README.md#error-message-enum)
  - []()

- __How to use Java__
  - [List](https://github.com/jbilee/woowa-test-notes/edit/main/README.md#list)
  - [Date object](https://github.com/jbilee/woowa-test-notes/edit/main/README.md#date-object)
  - [Stream](https://github.com/jbilee/woowa-test-notes/edit/main/README.md#stream)
  - [Text formatting](https://github.com/jbilee/woowa-test-notes/edit/main/README.md#text-formatting)

- __[File directory](https://github.com/jbilee/woowa-test-notes/edit/main/README.md#file-directory)__

# Snippets

## MissionUtils import
```
import camp.nextstep.edu.missionutils.Console;
```
```
import camp.nextstep.edu.missionutils.Randoms;
```
```
import camp.nextstep.edu.missionutils.DateTimes;
```

## Read input
```
public class InputView {
    public String readOrder() {
        System.out.println(InputPrompts.PURCHASE.getPrompt());
        return Console.readLine().strip();
    }
}
```

## Output view
```
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

## Validator
```
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
```
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
```
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
```
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

# Java notes
## List
Create a list
```
List<String> numbers = List.of(lottoNumbers.split(LOTTO_SPLIT_DELIMITER));
```
## Date object
## Stream
List-stream-list conversion
## Text formatting
```
String.format("%,d)
```

# File directory
![image](https://github.com/user-attachments/assets/1e77df19-d61a-40f4-8ccf-6803b6fc2131)
