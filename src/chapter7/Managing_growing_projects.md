# 패키지(Package), 크레이트(Crate), 모듈(Module)을 사용하여 프로젝트 성장시키기

이번 장에서는 패키지(package)와 크레이트(crate)를 사용하여 코드를 구성하는 방법을 배울 것입니다. 또한 모듈(module) 시스템을 사용하여 코드의 프라이버시(privacy)를 제어하고, 관련 기능을 함께 구성하며, 코드를 재사용하기 쉽게 만드는 방법을 배울 것입니다. 마지막으로, 외부 크레이트(external crate)를 사용하는 방법과 크레이트를 공개 저장소(public repository)에 배포하는 방법을 배울 것입니다.

패키지(package)와 크레이트(crate)는 코드를 구성하고 공유하는 방법을 제공합니다. 크레이트(crate)는 컴파일할 수 있는 최소한의 코드 단위입니다. 크레이트는 바이너리(binary) 또는 라이브러리(library)가 될 수 있습니다. 바이너리 크레이트(binary crate)는 컴파일하여 실행 파일을 생성하는 프로그램입니다. 라이브러리 크레이트(library crate)는 다른 프로그램에서 사용할 수 있는 코드를 포함합니다. 크레이트(crate)는 자체적인 스코프(scope)를 가지며, 이는 크레이트 내에서 사용되는 이름이 다른 크레이트의 이름과 충돌하지 않도록 합니다.

모듈(module)은 코드를 논리적으로 구성하고, 이름 충돌(name collision)을 방지하고, 코드의 프라이버시(privacy)를 제어하는 데 도움이 됩니다. 모듈(module)은 항목(items)을 그룹화하는 데 사용됩니다. 항목(items)은 함수(function), 구조체(struct), 열거형(enum), 트레이트(trait), 모듈(module) 등입니다.

패스(path)는 항목(item)을 찾는 방법입니다. 절대 경로(absolute path)는 크레이트(crate) 루트(root)에서 시작하며, 상대 경로(relative path)는 현재 모듈(module)에서 시작합니다. 항목(item)을 참조할 때는 항목(item)이 공개(public)되어 있어야 합니다.

`use` 키워드는 항목(item)을 스코프(scope)로 가져오는 데 사용됩니다. `pub use`는 항목(item)을 다시 내보내는 데 사용됩니다. `as` 키워드는 항목(item)을 다른 이름으로 가져오는 데 사용됩니다. 중첩된 경로(nested path)를 사용하여 여러 항목(items)을 한 번에 가져올 수 있습니다. 글로브 연산자(glob operator) `*`를 사용하여 모든 공개 항목(public items)을 가져올 수 있습니다.

외부 크레이트(external crate)를 사용하려면 `Cargo.toml` 파일에 의존성(dependency)을 추가해야 합니다. 크레이트(crate)를 공개 저장소(public repository)에 배포하려면 `cargo publish` 명령을 사용해야 합니다. 