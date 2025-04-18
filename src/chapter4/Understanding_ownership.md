# Rust의 소유권 개념(Ownership)

소유권(ownership)은 Rust의 가장 독특한 특징이며, 언어의 다른 부분들에 깊은 영향을 미칩니다.
소유권 덕분에 Rust는 가비지 컬렉터(garbage collector) 없이도 메모리 안전성을 보장할 수 있습니다.
따라서 소유권이 어떻게 작동하는지 이해하는 것이 중요합니다.
이 장에서는 소유권과 관련된 여러 기능인 빌림(borrow), 슬라이스(slice), 그리고 Rust가 메모리에 데이터를 어떻게 배치하는지에 대해 다룰 것입니다.