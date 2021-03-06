## typescript 简洁使用


*做最简洁核心的记录，可以节约时间、再是提炼概括，理解归纳、便于日后查阅联想*


> typescript原则之一： 对值所具有的结构进行类型检查

#### 基础类型

* boolean

	`let isDone: boolean = false;`
	
* number

	`let hexLiteral: umber = 7`
	
* string
	
	`let name: string = 'bob'`
	
* anrray

	`let list: number[] = [1,2,3,4]`
	`let list: Array<number> = [1,2,3,4]`
	
* Tuple 元组

	> 允许定义一个已知元素数量和类型的数组，各元素类型不必相同
	
		let x: [string, number];
		x = ['hello', 10];  ok
		x = [10, 'hello']; error
		x[0].substr(1); ok
		x[0].substr(1); error 'number' does not have 'substr'
	
* enum 枚举

	> 可以为一组数值富裕有好的名字，还可以由枚举的值查找相应的值
	
		enum Color {
			Red = 1,
			Green = 'green',
			Blue = 'blue'
		}
		let c: Color = Color.Green;
		let colorName: string = Color[2];

	

* Any 
	
	> 对于不清楚的类型，不希望类型检查器对这些值进行检查而是直接编译
	
		let notSure: any = 4;
		notSure = 'maybe a string instead'
		notSure = false;  ok
		
		let list = any[] = [1, true, 'free']
		list[1] = 100
		
* Void

	> 表示没有任何返回值
	
		function warnUser(): void {
			alert('...')
		}
		
###### 类型断言

> 可以通过类型断言的方式，告诉编译器，我确定这是什么，不需要进行检查了。

	1.尖括号<>
		let someValue: any = 'this is a string';
		let stringLen: number = (<string>someValue).length;
	
	2. as
		let stringLen: number = (someValue as string).length;

	
#### 接口 interface

> 为类型命名和代码定义契约

	interface LabelValue {
		label: string;
		color? string;
		[propName: string]: any;
	}
	
	function printLabel(labelObj: LabelValue) {
		console.log(labelObj.label);
	}
	
	let myObj = {size: 10, label: 'size 10 object'};
	printLabel(myObj);
	
> 要求printLabel传入的参数对象，必须有一个类型为string的label属性，color可传可不传，和另外不确定的属性，也可以定义函数类型
	
	interface SearchFun {
		someFun(source: string, subString: string): boolean;
		reset(): void;
	}
	
> 可索引的类型

	interface ReadonlyStringArray {
		readonly [index: number]: string;
	}
	let myArray: readonlyStringArray = ['Allice', 'bob'];
	myArray[2] = 'Mallory';  error
		

#### 类

	class Greeter {
		static standardGreeting = 'Hello, there';
		greeting: string;
		greet() {
			if (this.greeting) {
				return 'hello, ' + this.greeting;
			} else {
				return Greeter,standardGreeting;
			}
		}
	}
	
	let greeter1: Greeter;
	greeter1 = new Greeter();
	
	let freeterMarker: typeof Greeter = Greeter; 
	 //typeof Greeter: 表示取Greeter的类型，而不是实力的类型（包含了类的所有静态成员和构造函数）
	greeterMarker.standardGreeting = 'hey there!';
	let greeter2: Greeter = new greeterMaker();
	
#### 函数

> 为函数定义类型

	let myAdd = function(x: number, y:number): number {return x + y};
	
> 可选参数、默认参数、剩余参数

	function(firstName: string, lastName = "smith", age?: number, ...restOfName: string[]){}
	
#### 泛型

> 使用泛型来创建可重用组件，一个组件可以支持多种类型的数据
> 需要一种方法使返回值的类型与传入参数的类型是相同的，这里使用*类型变量*，表示的是类型而不是值。

	function identity<T>(arg: T): T {
		console.log(arg.length)  // error:T dont have .length (泛型约束)
		return arg;
	}
	// 给函数添加了类型变量T，（帮助捕获用户传入的类型，如string），再次使用T当做返回值类型，那可以知道参数类型和返回值类型是相同的了
	// 使用1:明确制定了T是string类型
	let output = identity<string>('myString');
	// 使用2：类型推论（编译器会根据参数自动确定T的类型）
	let output = identity('myString')
	
> 使用泛型变量

	function loggingIdentity<T>(arg: T[]): T[] {
		console.log(arg.length);
		return arg;
	}

#### 枚举 enum

> 用于定义一些带名字的常量，可以清晰的表达意图或创建一组有区别的用例

* 数字枚举

		enum Direction {
			Up = 1,
			Down,
			Left,
			Right
		}
		// 初始化Up为1，其余开始自增长；也可以不指定值，则从0开始记值
		function respond(recipient: string, message: Direction): void {}
		respond('princess Caroline', Direction.left)
		
* 字符串枚举

		enum Direction {
    		Up = "UP",
    		Down = "DOWN",
    		Left = "LEFT",
    		Right = "RIGHT",
		}
		
* 常量枚举

> 为了避免在额外生成的代码上的开销和额外的非直接的对枚举成员的访问，我们可以使用 const枚举。 常量枚举通过在枚举上使用 const修饰符来定义。

		const enum Directions {
    		Up,
    		Down,
    		Left,
    		Right
		}

		let directions = [Directions.Up, Directions.Down, Directions.Left, 		Directions.Right]

		var directions = [0 /* Up */, 1 /* Down */, 2 /* Left */, 3 /* Right */];

* 交叉类型

> 是将多个类型合并为一个类型，把现有的多种类型叠加到一起成为一种类型，包含所有类型的特性
> 如 Person & Serializable & Loggable

	function extend<T, U>(first: T, second: U): T & U {
    	let result = <T & U>{};
    	for (let id in first) {
        	(<any>result)[id] = (<any>first)[id];
    	}
    	for (let id in second) {
        	if (!result.hasOwnProperty(id)) {
            	(<any>result)[id] = (<any>second)[id];
        }
    }
    	return result;
	}


* 联合类型

> 表示一个值可以是几种类型之一，使用（|）分割，

	function padLeft(value: string, padding: string | number) {
    // ...
	}
	
* 类型保护和类型断言

> 由于可以为null的类型是通过联合类型实现，那么你需要使用类型保护来去除 null。 幸运地是这与在JavaScript里写的代码一致：

	function f(sn: string | null): string {
    	if (sn == null) {
        	return "default";
    	}else {
        	return sn;
    	}
	}
	// ||
	function f(sn: string | null): string {
    	return sn || "default";
	}
	
	// 类型断言手动去除： 添加 ！ 后缀
	function fixed(name: string | null): string {
	    return name!.charAt(0) + '.  the ' + epithet;
	 }
	 
* 类型别名 type

> 类型别名有时和接口很像，但是可以作用于原始值，联合类型，元组以及其它任何你需要手写的类型。

	type Name = string;
	type NameResolver = () => string;
	
* 索引类型

> 通过使用 索引类型查询 和 索引访问操作符

	function pluck<T, K extends keyof T>(o: T, names: K[]): T[K][] {
		return names.map(n => o[n]);
	}
	
	interface Person {
		name： string;
		age: number;
	}
	let strings: string[] = pluck(person, ['name])
	
	
> keyof T: 索引类型查询操作符（对于任何类型T，keyof T的结果为T上已知的公共属性名的联合）

	let personProps: keyof Person;  // 'name' | 'age'
	
> 第二个操作符是 T[K], 索引访问操作符。

	function getProperty<T, K extends keyof T>(o: T, name: K): T[K] {
		return o[name];
	}
	
> 索引类型和字符串索引签名

	interface Map<T> {
		[key: string]: T;
	}
	let keys: keyof Map<number>; // string;
	let value: Map<number>['foo]; // number;
	
	type Keys = 'option1' | 'option2';
	type Flags = { [K in Keys]: boolean };
	