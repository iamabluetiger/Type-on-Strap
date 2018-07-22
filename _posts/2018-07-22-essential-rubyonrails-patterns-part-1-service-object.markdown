---
layout: post
title: '[번역] Ruby on Rails, 필수 패턴 - part 1 : 서비스 객체'
date: 2018-07-22 18:04:53 +09:00
tags: [ruby, rails, pattern]
---

서비스 객체(서비스라고도 함)는 Ruby on Rails 개발에서 뚱뚱한 ActiveRecord 모델을 분해하고, 컨트롤러를 슬림하고 읽기 쉬운 상태로 유지하도록 돕는 최고의 수단입니다.

> 본 글은 [Essential RubyOnRails patterns — part 1: Service Objects](https://medium.com/selleo/essential-rubyonrails-patterns-part-1-service-objects-1af9f9573ca1)의 원문을 번역한 것입니다. 번역 실력이 그리 좋진 못해서 중간중간 정확하기보다는 의역처리 한게 있을 수도 있습니다. 미리 양해 부탁드립니다. 오역이 있다면 메일, 혹은 트윗 부탁드립니다.[^1]

### 언제 서비스 객체를 사용하나요?

이 패턴은 굉장히 간단하고 강력해서 정말 많이 사용하게 될 것입니다; 복잡한 액션, 많은 단계로 진행되는 프로세스, 콜백, 또는 어울리지 않는 다양한 모델끼리의 상호작용을 정의해야할 때 특히 유용합니다. 또한, 외부 클래스와의 상호작용 하는 모델 콜백 문제를 완화하는데도 주로 사용됩니다.[자세히...](http://samuelmullen.com/2013/05/the-problem-with-rails-callbacks/).

## 서비스 객체 패턴을 최대한 활용하려면 어떻게 해야하나요?

### #1 한가지의 네이밍 규칙을 고수하십시오.

프로그래밍에서 가장 어려운 일 중에 하나는 제대로 네이밍 하는 것입니다. 서비스 객체 네이밍은 주로 `UserCreator`, `TwitterAuthenticator`, `CodeObfuscator` 처럼 "or"로 끝나도록 하는 방법이 많이 쓰입니다. 하지만 이따금 `OrderCompleter` 처럼 예외사항이 생길 수 있습니다. 그래서 저는 조금 더 이해하기 쉽도록 커맨드 또는 액션명 뒤에 서비스 객체 이름을 넣는 방법을 사용하고 있습니다. `CreateUser`, `AuthenticateUsingTwitter`, `ObfuscateCode`, `CompleteOrder` -- 서비스 객체가 어떤 역할을 하는지 명확해집니다. 이 방법과 상관없이, 만약 하나의 네이밍 컨벤션을 쓰기로 했다면 여러가지를 섞어 쓰지 않도록 하세요. 일관성을 유지하십시오.

### #2 서비스 객체를 직접 인스턴트화 하지 마십시오.

보통 호출 메소드를 실행하기 위한 것 이외에는 서비스 객체 인스턴스를 많이 사용하지 않습니다. 이런 경우에는, 서비스 객체 호출의 표기법을 줄이기 위해 다음 추상화를 사용하는 것이 좋습니다.

```ruby
module Callable
  extend ActiveSupport::Concern
  
  class_methods do
    def call(*args)
      new(*args).call
    end
  end
end
```

이 추상화를 포함한 모듈은 `CreateUser.new(params).call` 또는 `CreateUser.new.call(params)` 를 `CreateUser.call(params)`로 표시할 수 있도록 간단하게 만들어줍니다. 이것은 좀더 짧고 읽기 좋습니다. 여전히 내부 객체를 가져올 필요가 있을 때 유용한 서비스 객체 인스턴트화를 하는 옵션이 남아 있게 됩니다.

### #3 서비스 객체를 호출하는 한가지 방법을 고수하십시오

개인적으로 `call` 메소드로 명명하는 것을 좋아하긴 하지만, 다른 방법을 이용하지 않을 특별한 이유는 없습니다 (`perform`, `run`, `execute` 등도 좋은 대안이다). 클래스 이름이 이미 클래스의 역할이 무엇인지 명시하고 있기 때문에 항상 같은 방식으로 호출하는 것이 중요합니다. 이보다 더 명확하게 할 필요는 없습니다. 이 접근법은 새로운 서비스 객체를 구현할 때마다 올바른 이름에 대해 생각할 필요가 없게하며, 다른 프로그래머에게도 서비스 구현을 들여다 보지 않고 서비스 객체를 사용하는 방법을 분명히 합니다.

### #4 하나의 서비스 객체에는 하나의 책임만 유지하십시오.

이 규칙은 서비스 객체를 호출하는 한 가지 방법을 고수함으로써 강제화되지만, 그러한 서비스 객체에서도 여러 가지 책임을 갖게 되는 것이 절대 불가능한 것은 아닙니다. 서비스 객체가 여러 액션을 조율하는데 탁월하지만 우리는 이러한 액션들 중 오직 한 세트만 실행되도록 해야합니다. 안티패턴을 예를 들면, `ManageUser` 서비스 객체는 사용자 생성 및 삭제를 담당합니다. 여기서 관리라는 것은 적합하지 않습니다; 실행될 액션을 어떻게 제어해야 하는지 명확하지 않습니다. 대신 `DeleteUser`, `CreateUser` 서비스 객체들을 사용하면 더 읽기 쉽고 자연스럽게 됩니다. 

### #5 서비스 객체 생성자를 간단하게 유지하십시오.

우리가 만들 클래스의 대부분에 있어서 생성자를 간단하게 유지하는 것은 좋은 생각입니다. 그러나 서비스를 호출하는 기본 방법이 호출 클래스 함수를 사용하는 경우 생성자가 서비스 인스턴스 변수의 인수만 저장하는 역할을 하도록 하는 것이 훨씬 더 좋습니다.

```ruby
class DeleteUser
  include Callable

  def initialize(user_id:)
    @user = User.find(user_id)
  end
  
  def call
    #…
  end
end
```

versus

```ruby
class DeleteUser
  include Callable  
  
  def initialize(user_id:)
    @user_id = user_id
  end
  
  def call
    #…
  end
  
  private
  
  attr_reader :user_id
  
  def user
    @user ||= User.find(user_id)
  end
end
```

생성자 대신 호출 메소드를 테스트하는 것에 집중할 수 있을 뿐 아니라 생성자에서 생성되는 것과, 아닌 것이 명확하게 구분됩니다. 개발자는 하루에 수십번도 의사결정을 해야하는데, 이 접근 방식을 표준화하여 일을 더실 수 있길 바랍니다.[^2]

### #6 호출 메소드의 인수들을 단순하게 유지하십시오.

서비스 객체에 두개 이상의 인수가 사용 되는 경우, 인수들을 이해하기 쉽게 하기 위하여 인수의 key 값을 명시하는 것이 좋습니다. 서비스 객체가 오직 하나의 인수만 받는다고 하더라도, 인수의 key 값을 명시하는 것은 더욱더 읽기 좋게 만듭니다.

```ruby
UpdateUser.call(params[:user], false)
```

versus

```ruby
UpdateUser.call(attributes: params[:user], send_notification: false)
```

### #7 State reader 를 통해서 결과값을 반환하십시오.

서비스 객체에서로부터 정보를 추출해내야만 하는 경우가 드물게 있을겁니다. 필요한 경우, 이 문제에 접근할 수 있는 몇 가지 방법이 있습니다. 서비스 객체는 호출 메소드의 결과를 반환할 수 있습니다. 예를 들어 `true`를 반환하면 성공적으로 실행되고 `false`를 반환하면 실패를 나타냅니다. 이 때, 호출 메소드가 서비스 객체 자체를 반환 할 때 훨씬 더 유연한 솔루션을 만들 수 있습니다. 이 방법으로 서비스 객체 인스턴스 상태를 읽는 것을 이용할 수 있습니다.

```ruby
update_user = UpdateUser.call(attributes: params[:user])

unless update_user.success?
  puts update_user.errors.inspect
end
```

예외가 일어날 수 있는 특정 상황에는 예외 처리를 하는 것이 더욱 효과적일 것입니다.

```ruby
begin
  UpdateUser.call(attributes: params[:user])
rescue UpdateUser::UserDoesNotExistException
  puts “User does not exist!”
end
```

### #8 가독성 있는 호출 메소드를 만드는 것에 집중하십시오.

호출 메소드는 서비스 객체의 핵심입니다. 최대한 가독성있게 작성하는 데 중점을 두는 것이 좋습니다. -- 바람직하게는 관련된 단계를 설명하고, 추가 로직을 최소화 하는 것입니다. 옵션으로써 `and`와 `or`를 사용하여 특정 단계의 흐름을 제어할 수 있습니다.

```ruby
class DeleteUser
  #…
  
  def call
    delete_user_comments
    delete_user and 
      send_user_deletion_notification
  end

private

  #…
end
```

### #9 트랜잭션에서 호출 메소드 래핑을 고려하십시오.

때때로 서비스 객체의 책임을 수행하기위하여 여러 단계를 포함하는 경우 트랜잭션 단계를 래핑하여 단계 중 하나라도 실패하면 이전 단계에서 변경 한 사항을 항상 롤백할 수 있도록 하는 것이 좋습니다.

### #10 Namespace로 서비스 객체들을 그룹화 하십시오.

조만간 수만 가지의 서비스 대상을 갖게 될 수 있습니다. 코드 구성을 개선하기 위해서는 공통 서비스 객체를 네임스페이스로 그룹화 하는 것이 좋습니다. 이러한 네임스페이스는 외부 서비스, 고급 기능들, 또는 우리가 생각하는 중요성에 따라 서비스 객체를 그룹화할 수 있습니다. 여전히 주요 목표는 서비스 객체 이름과 위치를 간단하고 읽기 쉬운 상태로 유지하는 것입니다. 하나의 컨벤션을 고수하는 것은 적절한 곳에서 빠르게 결정하도록 만들어줍니다 -- 당신의 선택의 폭을 줄일 수 있도록 하십시오.

### 결론

서비스 객체는 테스트하기 쉽고 광범히하게 사용될 수 있는 간단하고 강력한 패턴입니다. 구현의 용이성은 구현을 통제하에 두는 것에 대한 위협히이고 합니다. 서비스 객체의 이름을 지정할 때 간결한 규칙을 사용하면 동일한 방식으로 서비스 객체를 일관되게 호출하고 결과를 얻는 것은 물론, 서비스 객체 클래스의 내부를 간단하고 읽기 쉬운 상태로 유지함으로써 당신의 코드베이스는 굉장히 좋아질 것입니다.

서비스 객체 패턴에 대해 간단한 추상화가 필요하다고 생각되면 [BusinessProcess](https://github.com/Selleo/business_process) gem 을 참고하시거나, 정말 비슷한 [Use Case](https://webuild.envato.com/blog/a-case-for-use-cases/) 패턴을 참고하십시오. 더 얇은 레이어는 [rails-patterns](https://github.com/Selleo/pattern) 를 참고하십시오.

[Go to part 2 — Query Objects](iamabluetiger.com/2018/07/22/essential-rubyonrails-patterns-part-1-service-object.html) # 추후 번역 예정

---

[^1]: 메일 주소 : qkrcjdgh1004@gmail.com, 트위터 계정 : ilovech_
[^2]: why not take one off our plate by standardizing our approach.

