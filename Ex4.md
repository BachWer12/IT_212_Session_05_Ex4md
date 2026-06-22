# Bài làm Exercise 4 - Session 05

## Đáp án lựa chọn
**Phương án B**

## Phân tích lý do chọn Phương án B

### 1. Áp dụng kỹ thuật học nâng cao
Prompt B kết hợp ba kỹ thuật học tập nâng cao:
- **Giải thích đa cấp độ**: Mô tả AOP ở hai cấp độ (cấp độ 1 với ví dụ życia thực như bộ lọc camera hoặc trạm kiểm soát giao thông; cấp độ 2 với các khái niệm Aspect, JoinPoint, Pointcut, Advice). Điều này giúp người học nắm bản chất trước khi đi vào chi tiết.
- **So sánh khái niệm**: Lập bảng so sánh giữa AOP và OOP về mục đích và khả năng giải quyết vấn đề cắt ngang (cross-cutting concerns). So sánh này làm rõ cuando AOP cần được sử dụng thay vì пытаться giải quyết vấn đề bằng OOP truyền thống.
- **Ví dụ thực tiễn**: Cung cấp đoạn mã Spring Boot ngắn gọn sử dụng @Aspect và @Before/@After để tự động ghi log thời gian thực thi, kèm chú thích chi tiết bằng tiếng Việt. Điều này chuyển đổi lý thuyết thành mã code có thể chạy ngay.

### 2. Cấu trúc học tập hiệu quả
Kết hợp cả ba kỹ thuật trên giúp:
- Tăng cường ghi nhớ kiến thức nhờ việc liên tục chuyển từ abstraction xuống concrete example và ngược lại.
- Giảm thiểu sự hiểu lầm khi chỉ học bằng definición hoặc chỉ code mà không hiểu background.

## Phân tích lý do loại trừ các phương án còn lại

### Phương án A
- **Nhược điểm**: Yêu cầu chỉ "giải thích Spring AOP là gì và viết ví dụ ghi log". Prompt quá chung và không cấp phép hướng dẫn chi tiết. AI có thể trả về định nghĩa khô khan từ tài liệu và một ví dụ AOP chung chung, không giúp người học phân cấp hiểu biết (từ cơ bản tới nâng cao) và không làm rõ điểm khác biệt giữa AOP và OOP.

### Phương án C
- **Nhược điểm**: Yêu cầu viết class Aspect và giải thích cấu hình pom.xml. Prompt này bỏ qua bước giải thích bản chất khái niệm AOP, dẫn tới tình trạng sinh viên có thể copy-paste code nhưng không hiểu luồng chạy ngầm (nguyên tắc weaving, pointcut matching, advice types). Khi gặp tình huống thay đổi (ví dụ: muốn sử dụng @Around thay vì @Before) họ sẽ mắc kẹt.

## Mã nguồn Java Aspect ghi log do AI sinh ra dựa trên Prompt B
```java
package com.example.demo.aspect;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class LoggingAspect {

    private static final Logger log = LoggerFactory.getLogger(LoggingAspect.class);

    /**
     * Ghi log trước và sau khi thực thi mọi phương thức trong các lớp Service.
     * Pointcut: tất cả các phương thức trong các lớp có tên kết thúc bằng ServiceImpl
     * hoặc trong package service (tùy chọn thay đổi tùy dự án).
     */
    @Around("within(com.example.demo.service..*)")
    public Object logServiceMethod(ProceedingJoinPoint joinPoint) throws Throwable {
        String methodName = joinPoint.getSignature().toShortString();
        log.info(">>> Bắt đầu thực thi phương thức: {}", methodName);

        long startTime = System.nanoTime();
        Object result;
        try {
            result = joinPoint.proceed(); // thực thi phương thức mục tiêu
        } catch (Throwable ex) {
            log.error(">>> Phương thức {} ném ngoại lệ: {}", methodName, ex.toString(), ex);
            throw ex; // ném lại exception để không swallow
        }
        long elapsedTime = System.nanoTime() - startTime;
        log.info(">>> Kết thúc phương thức: {} - Thời gian thực thi: {} ms", methodName, elapsedTime / 1_000_000);

        return result;
    }
}
```
**Giải thích mã nguồn:**
- `@Aspect` đánh dấu đây là một khía cạnh.
* `@Component` để Spring tự động phát hiện và注入 bean này.
- `@Around("within(com.example.demo.service..*)")` áp dụng cho mọi phương thức trong package `service` và các sub-package.
- Trong advice:
  * Ghi log trước khi gọi `proceed()`.
  * Đo thời gian thực thi bằng `System.nanoTime()`.
  * Bat và log bất kỳ exception nào xảy ra, sau đó重新抛出.
  * Ghi log sau khi hoàn thành, bao gồm thời gian thực thi (ms).
- Sử dụng SLF4J để logging, tương thích với Logback hoặc Log4j2 phổ biến trong Spring Boot.