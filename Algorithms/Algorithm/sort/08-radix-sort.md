## 8. 基数排序

```java
public static int[] radix_sort(int[] arr) {
    int[] cnt = new int[65536];
    int[] temp = new int[arr.length];
    for (int num : arr) cnt[num % 65536]++;
    for (int i = 1; i < 65536; i++) cnt[i] += cnt[i - 1];
    for (int i = arr.length - 1; i >= 0; i--) temp[--cnt[arr[i] % 65536]] = arr[i];
    Arrays.fill(cnt, 0);
    for (int num : temp) cnt[num / 65536]++;
    for (int i = 1; i < 65536; i++) cnt[i] += cnt[i - 1];
    for (int i = arr.length - 1; i >= 0; i--) arr[--cnt[temp[i] / 65536]] = temp[i];
    return arr;
}
```