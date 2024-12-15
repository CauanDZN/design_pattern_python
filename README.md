# Design Patterns em Python

Os *design patterns* são soluções gerais e reutilizáveis para problemas recorrentes em design de software. Eles são divididos em três categorias principais:

- **Padrões Criacionais**: Relacionados à criação de objetos.
- **Padrões Estruturais**: Relacionados à estrutura de classes e objetos.
- **Padrões Comportamentais**: Relacionados à interação e responsabilidade entre objetos.

Aqui está uma explicação detalhada dos 24 padrões com exemplos em Python:

## Padrões Criacionais

### 1. Singleton
Garante que uma classe tenha apenas uma instância e fornece um ponto global de acesso a ela.
```python
class Singleton:
    _instance = None

    def __new__(cls, *args, **kwargs):
        if not cls._instance:
            cls._instance = super().__new__(cls)
        return cls._instance

# Exemplo de uso
singleton1 = Singleton()
singleton2 = Singleton()
print(singleton1 is singleton2)  # True
```

### 2. Factory Method
Define uma interface para criar objetos, mas permite que as subclasses decidam qual classe instanciar.
```python
from abc import ABC, abstractmethod

class Animal(ABC):
    @abstractmethod
    def speak(self):
        pass

class Dog(Animal):
    def speak(self):
        return "Woof!"

class Cat(Animal):
    def speak(self):
        return "Meow!"

class AnimalFactory:
    @staticmethod
    def get_animal(animal_type):
        if animal_type == "dog":
            return Dog()
        elif animal_type == "cat":
            return Cat()
        raise ValueError("Unknown animal type")

# Exemplo de uso
animal = AnimalFactory.get_animal("dog")
print(animal.speak())  # Woof!
```

### 3. Abstract Factory
Fornece uma interface para criar famílias de objetos relacionados ou dependentes sem especificar suas classes concretas.
```python
class Dog:
    def speak(self):
        return "Woof!"

class Cat:
    def speak(self):
        return "Meow!"

class PetFactory:
    def create_dog(self):
        return Dog()

    def create_cat(self):
        return Cat()

# Exemplo de uso
factory = PetFactory()
dog = factory.create_dog()
cat = factory.create_cat()
print(dog.speak())  # Woof!
print(cat.speak())  # Meow!
```

### 4. Builder
Constrói um objeto complexo passo a passo.
```python
class Car:
    def __init__(self):
        self.make = None
        self.model = None
        self.color = None

class CarBuilder:
    def __init__(self):
        self.car = Car()

    def set_make(self, make):
        self.car.make = make
        return self

    def set_model(self, model):
        self.car.model = model
        return self

    def set_color(self, color):
        self.car.color = color
        return self

    def build(self):
        return self.car

# Exemplo de uso
builder = CarBuilder()
car = (builder.set_make("Toyota")
              .set_model("Corolla")
              .set_color("Blue")
              .build())
print(car.__dict__)  # {'make': 'Toyota', 'model': 'Corolla', 'color': 'Blue'}
```

### 5. Prototype
Permite a cópia de objetos existentes sem torná-los dependentes de suas classes.
```python
import copy

class Prototype:
    def __init__(self, value):
        self.value = value

    def clone(self):
        return copy.deepcopy(self)

# Exemplo de uso
prototype = Prototype([1, 2, 3])
clone = prototype.clone()
clone.value.append(4)
print(prototype.value)  # [1, 2, 3]
print(clone.value)      # [1, 2, 3, 4]
```

## Padrões Estruturais

### 6. Adapter
Permite que interfaces incompatíveis trabalhem juntas.
```python
class EuropeanSocket:
    def voltage(self):
        return 220

class USASocketAdapter:
    def __init__(self, socket):
        self.socket = socket

    def voltage(self):
        return self.socket.voltage() / 2

# Exemplo de uso
socket = EuropeanSocket()
adapter = USASocketAdapter(socket)
print(adapter.voltage())  # 110
```

### 7. Bridge
Separa a abstração da implementação para que possam variar independentemente.
```python
class DrawingAPI1:
    def draw_circle(self, x, y, radius):
        print(f"API1: Drawing a circle at ({x}, {y}) with radius {radius}")

class DrawingAPI2:
    def draw_circle(self, x, y, radius):
        print(f"API2: Drawing a circle at ({x}, {y}) with radius {radius}")

class Circle:
    def __init__(self, x, y, radius, drawing_api):
        self.x = x
        self.y = y
        self.radius = radius
        self.drawing_api = drawing_api

    def draw(self):
        self.drawing_api.draw_circle(self.x, self.y, self.radius)

# Exemplo de uso
circle1 = Circle(1, 2, 3, DrawingAPI1())
circle2 = Circle(4, 5, 6, DrawingAPI2())
circle1.draw()
circle2.draw()
```

### 8. Composite
Compõe objetos em estruturas de árvore para representar hierarquias.
```python
class Component:
    def operation(self):
        pass

class Leaf(Component):
    def __init__(self, name):
        self.name = name

    def operation(self):
        return self.name

class Composite(Component):
    def __init__(self):
        self.children = []

    def add(self, component):
        self.children.append(component)

    def operation(self):
        return " + ".join([child.operation() for child in self.children])

# Exemplo de uso
leaf1 = Leaf("Leaf1")
leaf2 = Leaf("Leaf2")
composite = Composite()
composite.add(leaf1)
composite.add(leaf2)
print(composite.operation())  # Leaf1 + Leaf2
```

### 9. Decorator
O padrão **Decorator** permite adicionar comportamento a objetos de forma dinâmica. Ele é útil quando você deseja evitar a criação de subclasses para cada combinação possível de comportamentos.

```python
# Interface comum
class Component:
    def operation(self):
        pass

# Componente concreto
class ConcreteComponent(Component):
    def operation(self):
        return "ConcreteComponent"

# Decorador base
class Decorator(Component):
    def __init__(self, component):
        self.component = component

    def operation(self):
        return self.component.operation()

# Decoradores concretos
class DecoratorA(Decorator):
    def operation(self):
        return f"DecoratorA({self.component.operation()})"

class DecoratorB(Decorator):
    def operation(self):
        return f"DecoratorB({self.component.operation()})"

# Exemplo de uso
component = ConcreteComponent()
decorated1 = DecoratorA(component)
decorated2 = DecoratorB(decorated1)
print(decorated2.operation())  # DecoratorB(DecoratorA(ConcreteComponent))
```

### 10. Facade
O padrão **Facade** fornece uma interface simplificada para um sistema complexo. Ele oculta os detalhes internos e facilita o uso do sistema por clientes.

```python
# Subsistemas complexos
class SubsystemA:
    def operation_a(self):
        return "SubsystemA: Ready"

class SubsystemB:
    def operation_b(self):
        return "SubsystemB: Go"

# Fachada
class Facade:
    def __init__(self):
        self.subsystem_a = SubsystemA()
        self.subsystem_b = SubsystemB()

    def operation(self):
        results = [
            self.subsystem_a.operation_a(),
            self.subsystem_b.operation_b()
        ]
        return " | ".join(results)

# Exemplo de uso
facade = Facade()
print(facade.operation())  # SubsystemA: Ready | SubsystemB: Go
```

### 11. Flyweight
O padrão **Flyweight** economiza memória compartilhando o estado entre vários objetos semelhantes. Ele é ideal para lidar com um grande número de objetos de forma eficiente.

```python
# Flyweight compartilhado
class Flyweight:
    def __init__(self, intrinsic_state):
        self.intrinsic_state = intrinsic_state

    def operation(self, extrinsic_state):
        return f"Flyweight: {self.intrinsic_state} - {extrinsic_state}"

# Fábrica de Flyweights
class FlyweightFactory:
    def __init__(self):
        self.flyweights = {}

    def get_flyweight(self, key):
        if key not in self.flyweights:
            self.flyweights[key] = Flyweight(key)
        return self.flyweights[key]

# Exemplo de uso
factory = FlyweightFactory()
flyweight1 = factory.get_flyweight("SharedState1")
flyweight2 = factory.get_flyweight("SharedState2")
print(flyweight1.operation("UniqueState1"))  # Flyweight: SharedState1 - UniqueState1
print(flyweight2.operation("UniqueState2"))  # Flyweight: SharedState2 - UniqueState2
```

### 12. Proxy
O padrão **Proxy** fornece um substituto ou um intermediário para controlar o acesso a um objeto. Ele pode ser usado para implementar acesso controlado, carregamento preguiçoso, ou outra lógica intermediária.

```python
# Interface comum
class Subject:
    def request(self):
        pass

# Objeto real
class RealSubject(Subject):
    def request(self):
        return "RealSubject: Handling request"

# Proxy
class Proxy(Subject):
    def __init__(self):
        self.real_subject = None

    def request(self):
        if not self.real_subject:
            self.real_subject = RealSubject()
            print("Proxy: Inicializando RealSubject")
        return self.real_subject.request()

# Exemplo de uso
proxy = Proxy()
print(proxy.request())  # Proxy: Inicializando RealSubject
                        # RealSubject: Handling request
```

## Padrões Comportamentais

### 13. Chain of Responsibility
O padrão **Chain of Responsibility** permite que um pedido seja passado ao longo de uma cadeia de objetos, onde cada objeto da cadeia pode processar o pedido ou passá-lo para o próximo. Isso ajuda a desacoplar o remetente do receptor do pedido, permitindo que mais de um objeto tenha a oportunidade de tratá-lo.

```python
# Handler base
class Handler:
    def __init__(self, next_handler=None):
        self.next_handler = next_handler

    def handle(self, request):
        if self.next_handler:
            self.next_handler.handle(request)

# Concreto 1
class ConcreteHandlerA(Handler):
    def handle(self, request):
        if request == "A":
            print("Handler A processando o pedido")
        elif self.next_handler:
            self.next_handler.handle(request)

# Concreto 2
class ConcreteHandlerB(Handler):
    def handle(self, request):
        if request == "B":
            print("Handler B processando o pedido")
        elif self.next_handler:
            self.next_handler.handle(request)

# Exemplo de uso
handler_chain = ConcreteHandlerA(ConcreteHandlerB())
handler_chain.handle("A")  # Handler A processando o pedido
handler_chain.handle("B")  # Handler B processando o pedido
handler_chain.handle("C")  # Nenhum handler processa o pedido
```

### 14. Command
O padrão **Command** encapsula um pedido como um objeto, permitindo que você parametrize clientes com diferentes solicitações, enfileire ou registre pedidos e forneça suporte para operações de desfazer.

```python
# Command base
class Command:
    def execute(self):
        pass

# Comando Concreto
class LightOnCommand(Command):
    def __init__(self, light):
        self.light = light

    def execute(self):
        self.light.turn_on()

class LightOffCommand(Command):
    def __init__(self, light):
        self.light = light

    def execute(self):
        self.light.turn_off()

# Receptor
class Light:
    def turn_on(self):
        print("Light is ON")

    def turn_off(self):
        print("Light is OFF")

# Invoker
class RemoteControl:
    def __init__(self):
        self.command = None

    def set_command(self, command):
        self.command = command

    def press_button(self):
        self.command.execute()

# Exemplo de uso
light = Light()
light_on = LightOnCommand(light)
light_off = LightOffCommand(light)

remote = RemoteControl()

remote.set_command(light_on)
remote.press_button()  # Light is ON

remote.set_command(light_off)
remote.press_button()  # Light is OFF
```

### 15. Interpreter
O padrão **Interpreter** é usado para avaliar uma linguagem de expressão em um contexto específico. Ele define uma gramática para a linguagem e interpreta as sentenças dessa gramática.

```python
# Expressão base
class Expression:
    def interpret(self, context):
        pass

# Expressão numérica
class Number(Expression):
    def __init__(self, number):
        self.number = number

    def interpret(self, context):
        return self.number

# Expressão soma
class Add(Expression):
    def __init__(self, left, right):
        self.left = left
        self.right = right

    def interpret(self, context):
        return self.left.interpret(context) + self.right.interpret(context)

# Exemplo de uso
one = Number(1)
two = Number(2)
three = Number(3)

sum_expr = Add(one, Add(two, three))

print(sum_expr.interpret(None))  # 6
```

### 16. Iterator
O padrão **Iterator** permite acessar os elementos de um objeto agregado sequencialmente sem expor a sua representação interna.

```python
# Agregado
class BookCollection:
    def __init__(self):
        self.books = []
    
    def add_book(self, book):
        self.books.append(book)
    
    def __iter__(self):
        return iter(self.books)

# Exemplo de uso
collection = BookCollection()
collection.add_book("Book 1")
collection.add_book("Book 2")
collection.add_book("Book 3")

for book in collection:
    print(book)  # Book 1, Book 2, Book 3
```

### 17. Mediator
O padrão **Mediator** define um objeto que centraliza a comunicação entre objetos, evitando que eles se comuniquem diretamente. Isso reduz a dependência entre os objetos e simplifica a troca de informações.

```python
# Mediator
class Mediator:
    def send(self, message, colleague):
        pass

# Colleague base
class Colleague:
    def __init__(self, mediator):
        self.mediator = mediator

    def send(self, message):
        self.mediator.send(message, self)

    def receive(self, message):
        pass

# Colleague Concreto
class ConcreteColleagueA(Colleague):
    def receive(self, message):
        print(f"Colleague A received: {message}")

class ConcreteColleagueB(Colleague):
    def receive(self, message):
        print(f"Colleague B received: {message}")

# Mediator Concreto
class ConcreteMediator(Mediator):
    def __init__(self, colleagueA, colleagueB):
        self.colleagueA = colleagueA
        self.colleagueB = colleagueB
        self.colleagueA.mediator = self
        self.colleagueB.mediator = self

    def send(self, message, colleague):
        if colleague == self.colleagueA:
            self.colleagueB.receive(message)
        else:
            self.colleagueA.receive(message)

# Exemplo de uso
colleagueA = ConcreteColleagueA(None)
colleagueB = ConcreteColleagueB(None)
mediator = ConcreteMediator(colleagueA, colleagueB)

colleagueA.send("Hello B")  # Colleague B received: Hello B
colleagueB.send("Hello A")  # Colleague A received: Hello A
```

### 18. Memento
O padrão **Memento** permite capturar o estado interno de um objeto sem violar o encapsulamento, permitindo que o objeto retorne a esse estado mais tarde. Isso é útil para implementar operações de "desfazer".

```python
# Memento
class Memento:
    def __init__(self, state):
        self.state = state

# Originator
class Originator:
    def __init__(self):
        self.state = None

    def set_state(self, state):
        self.state = state
        print(f"State set to: {state}")

    def save_state_to_memento(self):
        return Memento(self.state)

    def restore_state_from_memento(self, memento):
        self.state = memento.state
        print(f"State restored to: {self.state}")

# Exemplo de uso
originator = Originator()
originator.set_state("State 1")
saved_state = originator.save_state_to_memento()

originator.set_state("State 2")
originator.restore_state_from_memento(saved_state)  # State restored to: State 1
```

### 19. State
O padrão **State** permite que um objeto altere seu comportamento quando seu estado interno muda. O objeto parecerá mudar sua classe. Ele é útil para situações onde o comportamento de um objeto depende de seu estado atual.

```python
# State base
class State:
    def handle(self):
        pass

# ConcreteStateA
class ConcreteStateA(State):
    def handle(self):
        print("Handling state A")

# ConcreteStateB
class ConcreteStateB(State):
    def handle(self):
        print("Handling state B")

# Context
class Context:
    def __init__(self, state: State):
        self.state = state

    def set_state(self, state: State):
        self.state = state

    def request(self):
        self.state.handle()

# Exemplo de uso
context = Context(ConcreteStateA())
context.request()  # Handling state A

context.set_state(ConcreteStateB())
context.request()  # Handling state B
```

### 20. Strategy
O padrão **Strategy** permite definir uma família de algoritmos, encapsulá-los e torná-los intercambiáveis. Ele permite que o algoritmo seja escolhido em tempo de execução.

```python
# Strategy base
class Strategy:
    def execute(self):
        pass

# ConcreteStrategyA
class ConcreteStrategyA(Strategy):
    def execute(self):
        print("Executing strategy A")

# ConcreteStrategyB
class ConcreteStrategyB(Strategy):
    def execute(self):
        print("Executing strategy B")

# Context
class Context:
    def __init__(self, strategy: Strategy):
        self.strategy = strategy

    def set_strategy(self, strategy: Strategy):
        self.strategy = strategy

    def execute_strategy(self):
        self.strategy.execute()

# Exemplo de uso
context = Context(ConcreteStrategyA())
context.execute_strategy()  # Executing strategy A

context.set_strategy(ConcreteStrategyB())
context.execute_strategy()  # Executing strategy B
```

### 21. Template Method
O padrão **Template Method** define o esqueleto de um algoritmo em um método, adiando a implementação de alguns passos para subclasses. Ele permite que subclasses redefinam certos passos de um algoritmo sem mudar sua estrutura.

```python
# Classe abstrata
class Game:
    def play(self):
        self.start()
        self.play_game()
        self.end()

    def start(self):
        pass

    def play_game(self):
        pass

    def end(self):
        pass

# Subclasse concreta
class Soccer(Game):
    def start(self):
        print("Starting the soccer game!")

    def play_game(self):
        print("Playing the soccer game!")

    def end(self):
        print("Ending the soccer game!")

# Exemplo de uso
soccer = Soccer()
soccer.play()
# Starting the soccer game!
# Playing the soccer game!
# Ending the soccer game!
```

### 22. Visitor
O padrão **Visitor** permite adicionar novas operações a objetos de uma estrutura sem modificar as classes desses objetos. Ele define uma nova operação que pode ser aplicada a cada tipo de elemento da estrutura.

```python
# Element base
class Element:
    def accept(self, visitor):
        pass

# Elemento Concreto
class ConcreteElementA(Element):
    def accept(self, visitor):
        visitor.visit(self)

class ConcreteElementB(Element):
    def accept(self, visitor):
        visitor.visit(self)

# Visitor base
class Visitor:
    def visit(self, element):
        pass

# Visitor Concreto
class ConcreteVisitor(Visitor):
    def visit(self, element):
        if isinstance(element, ConcreteElementA):
            print("Visiting ConcreteElementA")
        elif isinstance(element, ConcreteElementB):
            print("Visiting ConcreteElementB")

# Exemplo de uso
elementA = ConcreteElementA()
elementB = ConcreteElementB()

visitor = ConcreteVisitor()

elementA.accept(visitor)  # Visiting ConcreteElementA
elementB.accept(visitor)  # Visiting ConcreteElementB
```

### 23. Flyweight
O padrão **Flyweight** é usado para otimizar o uso de memória, compartilhando objetos comuns em vez de criar novos objetos para cada instância. Ele é útil quando há um grande número de objetos semelhantes e queremos evitar a duplicação.

```python
# Flyweight base
class Flyweight:
    def operation(self, extrinsic_state):
        pass

# ConcreteFlyweight
class ConcreteFlyweight(Flyweight):
    def __init__(self, intrinsic_state):
        self.intrinsic_state = intrinsic_state

    def operation(self, extrinsic_state):
        print(f"Flyweight with intrinsic state: {self.intrinsic_state} and extrinsic state: {extrinsic_state}")

# FlyweightFactory
class FlyweightFactory:
    def __init__(self):
        self.flyweights = {}

    def get_flyweight(self, intrinsic_state):
        if intrinsic_state not in self.flyweights:
            self.flyweights[intrinsic_state] = ConcreteFlyweight(intrinsic_state)
        return self.flyweights[intrinsic_state]

# Exemplo de uso
factory = FlyweightFactory()

flyweight1 = factory.get_flyweight("State1")
flyweight2 = factory.get_flyweight("State1")
flyweight3 = factory.get_flyweight("State2")

flyweight1.operation("External1")  # Flyweight with intrinsic state: State1 and extrinsic state: External1
flyweight2.operation("External2")  # Flyweight with intrinsic state: State1 and extrinsic state: External2
flyweight3.operation("External3")  # Flyweight with intrinsic state: State2 and extrinsic state: External3
```

### 24. Bridge
O padrão **Bridge** separa a abstração da implementação, permitindo que ambas evoluam de forma independente. Ele ajuda a evitar a rigidez quando novas implementações de abstrações ou implementações específicas precisam ser adicionadas.

```python
# Implementor
class Implementor:
    def operation_implementation(self):
        pass

# ConcreteImplementorA
class ConcreteImplementorA(Implementor):
    def operation_implementation(self):
        print("Implementation A")

# ConcreteImplementorB
class ConcreteImplementorB(Implementor):
    def operation_implementation(self):
        print("Implementation B")

# Abstraction
class Abstraction:
    def __init__(self, implementor: Implementor):
        self.implementor = implementor

    def operation(self):
        self.implementor.operation_implementation()

# RefinedAbstraction
class RefinedAbstraction(Abstraction):
    def operation(self):
        print("Refined operation")
        self.implementor.operation_implementation()

# Exemplo de uso
implementorA = ConcreteImplementorA()
implementorB = ConcreteImplementorB()

abstractionA = RefinedAbstraction(implementorA)
abstractionA.operation()  # Refined operation, Implementation A

abstractionB = RefinedAbstraction(implementorB)
abstractionB.operation()  # Refined operation, Implementation B
```