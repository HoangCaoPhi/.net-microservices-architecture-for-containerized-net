CQRS là một mẫu pattern ở phần kiến trúc, nó phân tách thành các mô hình đọc và ghi dữ liệu. Thuật ngữ liên quan [Command Query Separation (CQS)](https://martinfowler.com/bliki/CommandQuerySeparation.html) ban đầu được định nghĩa bởi Bertrand Meyer trong cuốn sách Object-Oriented Software Construction.

Về ý tưởng cơ bản là bạn chia hoạt động trên hệ thống của bạn thành hai loại tách biệt rõ ràng:

+ Queries: những queries trả về một kết quả và không làm thay đổi trạng thái của hệ thống, và chúng không gây ra side effects.

+ Commands: những commands sẽ thay đổi trạng thái của hệ thống.

CQR là một khái niệm đơn giản: nó đề cập đến các phương thức trong cùng một đối tượng chỉ là query hoặc command. Mỗi phương thức hoặc trả về trạng thái hoặc thay đổi trạng thái, nhưng không làm cả hai việc cùng một lúc. Ngay cả đối tượng repository pattern cũng có thể áp dụng CQS. CQS có thể được coi là nguyên tắc nền tảng của CQRS.

[Command and Query Responsibility Segregation (CQRS)](https://martinfowler.com/bliki/CQRS.html) được giới thiệu bởi Greg Young và được phát triển rộng rãi bởi Udi Dahan và những người khác. Nó dựa trên nguyên tắc CQS, mặc dù nó chi tiết hơn. Nó có thể coi là một pattern dựa trên commands và events và thêm các tùy chọn messages bất đồng bộ. Trong nhiều trường hợp sử dụng CQRS, hệ thống sẽ sử dụng các cơ sở dữ liệu khác nhau cho các hoạt động read (queries) và write (updates). Tuy nhiên, một số hệ thống CQRS phát triển hơn có thể triển khai [Event-Sourcing (ES)](https://martinfowler.com/eaaDev/EventSourcing.html) cho cơ sở dữ liệu update của bạn. Vì vậy, thay vì lưu trữ trạng thái hiện tại của dữ liệu, hệ thống sẽ lưu trữ tất cả các sự kiện đã xảy ra trong mô hình miền (domain model). Tuy nhiên, cách tiếp cận này không được sử dụng trong hướng dẫn này. Hướng dẫn này chỉ tiếp cận đơn giản về CQRS, nó chỉ bao gồm việc phân tách riêng biệt queries và comands.

CQRS sử dụng hai đối tượng riêng biệt cho hoạt động read/write, trong khi ở các ngữ cảnh khác thì chỉ sử dụng một đối tượng. Có nhiều lý do để chuẩn hóa riêng một cơ sở dữ liệu phục vụ cho việc read, nhưng chúng ta sẽ chưa sử dụng đến cách tiếp cận này, mà mục tiêu là có sự linh hoạt hơn trong các queries thay vì hạn chế chúng với các ràng buộc từ các mẫu thiết kế DDD như các aggregates.

Một ví dụ cơ bản là Ordering microservice từ ứng dụng [eShopOnContainers ](https://github.com/dotnet/eShop/tree/main). Service này implement một microservice dựa trên cách tiếp cận CQRS đơn giản. Nó sử dụng duy nhất một cơ sở dữ liệu, nhưng có hai mô hình logic cùng các mẫu thiết kế DDD cho các transactional domain, như hình dưới đây:

![image.png](https://learn.microsoft.com/en-us/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns/media/apply-simplified-microservice-cqrs-ddd-patterns/simplified-cqrs-ddd-microservice.png)

"Ordering" microservice bao gồm một cơ sở dữ liệu Ordering Database, nó có thể hoặc không cùng một Docker Host. Có một cơ sở dữ liệu cùng Docker Host có thể dễ dàng và không vấn đề gì trong môi trường Development, nhưng với môi trường Production thì không.

Application layer có thể bao gồm Web API. Khía cạnh thiết kế quan trọng ở đây là microservice đã phân chia thành hai hoạt động riêng biệt (Queries & View Models) cho việc read dữ liệu và (Commands & Domain-Model) cho việc write dữ liệu theo CQRS pattern.

Cách tiếp cận này giúp cho việc queries không phụ thuộc vào những hạn chế và ràng buộc của DDD pattern. Những ràng buộc của DDD sẽ chỉ có ý nghĩa trong các transactions và updates, sẽ được giải thích ở phần sau.

