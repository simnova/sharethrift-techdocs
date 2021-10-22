# Idempotent Messages

Azure Storage Queue -&#x20;

Where at all possible try to minimize side effects of processing queue items and being able to handle the same message sent multiple times.

For messages related to processing such as financial transactions, leverage high throughput/low cost data store such as Azure Table Storage to check if the message has already been processed by storing a history of processed transactions, note that this adds significant complexity.

