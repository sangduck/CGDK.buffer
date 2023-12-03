## CGDK::buffer란
압도적으로 빠르고 편리한 메시지 직렬화 버퍼 시스템<br/>
C++,C#간 고성능 게임 송수신을 위한 직렬화 버퍼 시스템<br/>
<br/>
<img src="https://github.com/sangduck/CGDK.buffer/blob/master/C%23/document/benchmark_20231203_2.png"/>
<br/>
protobuf, MessagePack,MemoryPack 등등 <br/>
c# 최고 성능을 제공한다는 직렬화 라이브러리들보다 수배에서 수십배의 압도적 성능을 제공합니다.<br/>
<br>

- CGD.buffer의  특징
   - 매우 간단(Append/Extract/SetFront/GetFront/GetSizeOf가 사실상 대부분...)<br>
   - 초고성능 (Reflection & Roslyn & Souce Generator로 제작되어 압도적 성능을 자랑합니다.)<br>
   - 작고 가볍습니다.<br>
   - Schemaless와 Schema(구조체 직렬화)를 모두 지원<br>
   - 설정이나 스크립트(IDL)없이 구조체를 그대로 Schema로 사용 가능<br>
   - 지원되는 자료형은<br>
      * 기본 자료형(char,byte,sbyte,int,uint,long,ulong,float,double)<br>
      * enum<br>
      * 문자열(string)<br>
      * 배열(T[N])<br>
      * 컬렉션(List<T>, Dictionary<K,V>)<br>
      * 시간(DateTime) <br>
      * CGDK.buffer<br>
      * struct<br>
      * class<br>
      * IBufferSerialzable<br>
   - Reflection과 Roslyn & Source Generator를 사용하여 구현하였습니다.<br>
     (Roslyn과 Source Generator의 성능 버프를 받으시려면 CGDK.buffer.Generator도 nuget으로 설치를 하셔야 합니다.)
   - C++ 버전인 CGDK::buffer와 호환됩니다.<br>
   - 단점. 현재는 프로젝트에 Unsafe 설정이 필요합니다.<br>
<br>
<br>

## CGD.buffer 직렬화/역직렬화 하기
### 1. 직렬화하기(Schemaless)  
   버퍼에 데이터 쓰기는 Append<T>(...)로 간단히 가능합니다.<br>
   (데이터형을 생략할 경우 인자의 자료형으로 간주합니다.)<br>

   ``` C#
   // CGDK.buffer를 생성하고 메모리 256byte를 할당합니다.
   var buf = new CGDK.buffer(256);
         
   buf.Append<byte>(10); // byte형 10을 직렬화합니다.
   buf.Append<sbyte>(20); // sbyte형 20을 직렬화합니다.
   buf.Append<int>(-100); // int형 -100을 직렬화합니다.
   buf.Append<uint>(1000); // uint형 1000을 직렬화합니다.
   buf.Append<string>("Test String");	// 문자열을 직렬화합니다.
   ```

### 2. 역직렬화하기(Schemaless)<br>
- Extract<T>() 함수로 간단히 역직렬화를 할 수 있습니다.<br>

   ``` C#
   var temp1 = buf.Extract<byte>(); // 10 => byte형 값을 역직렬화합니다.
   var temp2 = buf.Extract<sbyte>(); // 20 => sbyte형 값을 역직렬화합니다.
   var temp3 = buf.Extract<int>(); // -100 => int형 값을 역직렬화합니다.
   var temp4 = buf.Extract<uint>(); // 1000 => uint형 값을 역직렬화합니다.
   var temp5 = buf.Extract<string>(); // "Test String" => 문자열을 역직렬화합니다.
   ```

- 컬렉션 직렬화/역직렬화<br> 
C#의 List<T>, Dictionary<K,V> ... 등의 데이터도 직렬화/역직렬화 가능합니다.<br/>

   ``` C#
   List<int>                      listTest = new ...;
   List<string>                   listStringTest = new ...;
   Dictionary<string, int>        dicTest = new ...;
   Dictionary<int, List<string>>  dicListTest = new ...;
         
   CGD.buffer buf = new CGD.buffer(new byte[256]);
         
   buf.Append(listTest);
   buf.Append(listStringTest);
   buf.Append(dicTest);
   buf.Append(dicListTest);
         
   .................
   // 역직렬화
   var temp1 = buf.Extract<List<int>> ();
   var temp2 = buf.Extract<List<string>> ();
   var temp3 = buf.Extract<Dictionary<string,int>> ();
   var temp4 = buf.Extract<Dictionary<string,List<int>>> ();
   ```

### 3. 직렬화에 필요한 메모리 크기 구하기<br>
- 데이터를 직렬화 했을 때의 크기를 CGDK.GetSizeOf<V>(...)르 사용해 미리 얻을 수 있습니다. <br>

   ``` C#
   var maplistTemp = new Dictionary<int,string>();
   ...

   var size = CGDK.buffer.GetSizeOf(maplistTemp);
   ```
- 다만 GetSizeOf<T>()함수는 큰 비용은 아니지만 동적 처리 함수이므로 실시간 비용이 들어가므로 만큼 고려해 사용하시길 바랍니다.<br/>
<br>


### 4. 동적 메모리 할당 받기<br>
메모리는 3가지 방법으로 설정이 가능합니다.
- 생성자에서 바로 생성하기
   ``` C#
   // 1000byte 메모리를 동적 할당 받는다.
   var buf = new CGDK.buffer(1000);
   ```

- 생성 후에 할당하기
   ``` C#
   // 1000byte 메모리를 동적 할당 받는다.
   var buf = new CGDK.buffer();
   buf.Alloc(1000);
   ```

- 외부에서 생성해서 넣기
   ``` C#
   // 1000byte 메모리를 동적 할당 받는다.
   var temp = new byte[1000];

   var buf = new CGDK.buffer(temp);
   ```
   혹은 

   ``` C#
   // 1000byte 메모리를 동적 할당 받는다.
   var temp = new byte[1000];

   var buf = new CGDK.buffer();
   buf.SetBuffer(temp, 0, 0); // 0은 offset과 count값입니다.
   ```
<br/>
따라서 GetSizeOf와 함께 사용하면 직렬화 크기 맞는 버퍼를 할당 가능합니다.<br/>

``` C#
// 1000byte 메모리를 동적 할당 받는다.
TEST temp = new TEMP();
...

var buf = new CGDK.buffer(CGDK.buffer.GetSizeOf(temp));
buf.Append(temp);
```

할당받은 버퍼를 비우려면... __Clear()__를 하면 됩니다.
Clear()함수는 Offset과 Count만 0으로 만드는 함수가 아니라 앗사리 할당받은 메모리를 null로 초기화하는 함수입니다.
``` C#
// 1000byte 메모리를 동적 할당 받는다.
var buf = new CGDK.buffer();

// - 메모리를 할당받지 않았으니 empty == true
var is_empty1 = buf.IsEmpty(); // true

// - 256byte의 메모리를 할당받는다.
var buf.Alloc(256);

// - 당연히 empty == false!
var is_empty2 = buf.IsEmpty(); // false

// - 버퍼를 할당해제한다.
buf.Clear();

// - Clear()후면 Empty상태가 된다.
var is_empty3 = buf.IsEmpty(); // true
```
<br>

### 5. 읽어만 내기
- Extract<T>를 사용해 값을 읽어내면 그만큼의 오프셋과 크기가 변경됩니다.<br/>
  변경없이 값만 읽어 내고 싶다면 GetFront<T>([offset])을 사용하면 됩니다.

   ``` C#
   var buf = new CGDK.buffer(1024);
   buf.Append<int>(100);
   buf.Append<long>(200);
   buf.Append<string>("test");
   
   // 버퍼의 앞에서 4Byte Offset만큼 떨어진 곳에서 long값을 값을 읽어냅니다.
   var temp1 = buf.GetFront<long>(4); // temp는 200이 되겠죠.

   // 버퍼의 앞에서 12Byte Offset만큼 떨어진 곳에서 string값을 값을 읽어냅니다.
   var tenp2 = buf.GetFront<string>(12); // 
   ```

- GetFront로 변경된 Offset을 얻고 싶다면 __CGDK.Offset__을 사용하면 됩니다.<br/>
Offset을 사용해 GetFront<T>를 수행하면 읽어낸만큼 Offset값을 업데이트 해주므로 연속적으로 Offset을 사용해 값을 읽어낼 수 있습니다.<br/>

   ``` C#
   var buf = new CGDK.buffer(1024);
   buf.Append<int>(100);
   buf.Append<long>(200);
   buf.Append<string>("test");

   // Offset을 정의합니다.
   var temp_offset = new CGDK.Offset(4);

   // 버퍼의 앞에서 4Byte Offset만큼 떨어진 곳에서 long값을 값을 읽어옵니다.
   var temp1 = buf.GetFront<long>(ref temp_offset); // temp는 200이 되며 temp_offset값은 12로 변경됩니다.
   var tenp2 = buf.GetFront<string>(ref temp_offset); // "test"가 될 것이며 temp_offset값은 20로 변경됩니다.
   ```
- 주의) 타입에 대한 안정성을 제공하지는 않습니다. 즉 GetFront<int>(8); 하면 그냥 8Byte Offset이 떨어진 값을 int로 가정해서 읽어올 뿐입니다. 원래 데이터의 형이 int가 아닐 경우 원하지 않는 값을 얻을 수도 있습니다.<br/>
타입 안정성을 제공하지 않을 것을 인해 많은 사용상의 편리함은 있을 수 있습니다만 예외를 발생시킬 수도 있으면 잘 사용할 필요가 있습니다.<br>
<br>

### 6. 덥어 쓰기
- Append<T>는 버퍼읠 제일 끝에만 붙이는 처리입니다.<br/>
근데 이미 쓰여진 데이터를 덥어쓰거나 먼저 버퍼에 값을 써넣은 후 추후 값을 써넣어야 하는 경우도 있을 수 있습니다.<br/>
그럴 때 __SetFront<T>()__ 함수를 사용하시면 됩니다.<br>

   ``` C#
   var buf = new CGDK.buffer(1024);
   buf.Append<int>(100);
   buf.Append<long>(); // 일단 공간만 잡고 값을 쓰지 않았습니다.
   buf.Append<string>("test");
   
   // 추후에 Offset 4byte 떨어진 곳에 long 타입으로 값을 덥어 써줍니다. 
   // 여기서는 buf의 직렬화한 길이를 써넣었습니다.
   buf.SetFront<long>(4, buf.Count); // temp는 200이 되겠죠.
   ```
- SetFront<T>() 함수의 리턴값을 기본적으로 값을 써넣은 후에 변경된 Offset값을 리턴값으로 돌려줍니다.<br>
이 값을 받아 값을 읽거나 써넣는 데에 사용할 수 있지만 타입 안정성을 제공하지 않으므로 주의해서 사용할 필요가 있습니다.<br>
<br>

### 7. 구조체 직렬화
직렬화 대상 구조체는 '[CGDK.Attribute.Serializable]' Attribue를 붙혀 줘야 합니다.<br>
구조체가 static이 아니어야 하며 public 속성을 가져야 합니다.
구조체의 멤버 변수들은 기본적으로 모두 직렬화의 대상이 됩니다.
다만 public 속성이 아니거나 const, read_only,write_only 같은 속성이 가지면 직렬화에서 제외됩니다.
특별히 직렬화/역직렬화에서 제외하고 싶으면 '[CGDK.Attribute.Filed(false)]'를 붙혀주면 됩니다.

``` C#
[CGDK.Attribute.Serializable]
public struct TEST
{
    public int       x;
    public float     y;
    [CGDK.Attribute.Filed(false)]
    public string    z; // z는 직렬화에서 제외됩니다.
    public List<int> w;
};
	    
```
구조체 직렬화는 동적 할당을 하지 않아도 되기 때문에 성능상 이점이 있을 수 있지만 상속 할수 없다는 단점도 있습니다.<br>
<br>

### 8. 클래스 직렬화
직렬화 대상 클래서는 '[CGDK.Attribute.Serializable]' Attribue를 붙혀 줘야 합니다.<br>
클래스가 static이 아니어야 하며 public 속성을 가져야 합니다.
클래스의 멤버 변수는 기본적으로 직렬화의 대상이 아닌 것으로 간주합니다.
직렬화를 하고자 하는 멤버만 '[CGDK.Attribute.Filed]'를 붙혀줘야 합니다.
또 이 멤버는 public 속성이어야 하며 const, read_only,write_only 속성을 가져서는 안됩니다.
(단 '[CGDK.Attribute.Filed(false)]'를 붙혀 주면 직렬화를 하지 않습니다. 즉 Attribute를 안붙힌 것과 같습니다.)

``` C#
[CGDK.Attribute.Serializable]
public class TEST
{
   [CGDK.Attribute.Field]
    public int       x;
   [CGDK.Attribute.Field]
    public float     y;

    public string    z; // z는 직렬화에서 제외됩니다.

   [CGDK.Attribute.Field]
    public List<int> w;
};
	    
```
클래스 직렬화는 동적 할당을 해야 하므로 작고 빈번한 메시지 사용에는 약간 단점이 될수 있지만 상속이 가능하다는 장점이 있습니다.<br/>
<br/>
<br>

## CGD.buffer 구조<br/>
CGDK.buffer의 멤버변수는 buffer, m_offset, m_count 3개의 멤버 변수로 구성되어 있습니다.

   ``` C#
	// 1) Buffer
	private byte[]?		m_buffer;

	// 2) Buffer중 시작 위치
	private int			m_offset;

	// 3) 사용 길이 (m_iOffset부터 Buffer중 사용하는 끝까지의 길이)
	private int			m_count;
   ```
   - m_buffer는 byte의 배열이며 실제 데이터를 써넣은 배열 객체입니다.<br/>
   - m_offset은 데이터가 쓰여진 처음 위치의 offset을 말합니다.<br/>
     m_offset값이 5면 m_buffer[5]부터 데이터가 있다는 뜻입니다.<br/>
     Extract<T>()를 하면 읽어낸 Byte만큼 더해집니다.<br/>
   - m_count는 쓰여진 데이터의 바이트 수입니다.<br/>
     Append<T>(...)를 하면 쓰여진 Byte 수만큼 더해집니다.<br/>
<br/>

   즉, m_buffer 배열에서 m_offset부터 m_count만큼의 데이터가 존재한다는 의미입니다.<br/>
   <br/>
   이 값들은 다음과 같이 Offset과 Count 프라퍼티를 사용해 직접 읽거나 쓸 수도 있습니다.<br/>
   

   ``` C#
   var buf = new CGDK.buffer(256);

   var b1 = buf.Array;  // m_buffer 배열입니다.

   buf.Append<byte>(10);
   buf.Append<sbyte>(20);
   buf.Append<int>(-100);
   
   var s1 = buf.Count;  // s1값은 6일 것입니다.
   var f1 = buf.Offset; // f1값은 0일 것입니다.

   buf.Append<long>(1);

   var s2 = buf.Count;  // s2값은 14일 것입니다.
   var f2 = buf.Offset; // f2값은 0일 것입니다.

   buf.Extract<byte>();

   var s3 = buf.Count;  // s3값은 13일 것입니다.
   var f3 = buf.Offset; // f3값은 1일 것입니다.

   buf.Extract<sbyte>();

   var s4 = buf.Count;  // s4값은 12일 것입니다.
   var f4 = buf.Offset; // f4값은 2일 것입니다.

   buf.Extract<int>();

   var s5 = buf.Count;  // s5값은 8일 것입니다.
   var f5 = buf.Offset; // f5값은 6일 것입니다.

   buf.Extract<long>();

   var s6 = buf.Count;  // s6값은 0일 것입니다.
   var f6 = buf.Offset; // f6값은 14일 것입니다.      
   ```
   Count와 Offset 값을 대입을 통해 직접 변경은 가능하지만 되도록이면 직접 값을 써넣는 일은 피하시길 권장합니다.

<br/>
아래와 같은 함수들도 사용할 수 있습니다.<br/>

   ``` C#
   var buf = new CGDK.buffer(256);
         
   buf.Append<byte>(10);
   buf.Append<sbyte>(20);
   buf.Append<int>(-100);

   var f1 = buf.Capacity;     // 256 => 할당받은 배열의 크기를 구합니다.
   var s1 = buf.RemaindSize;  // 250 => 버퍼에서 남아 있는 크기를 구합니다.
   ```
<br/>
<br/>

## CGD.buffer 복사하기<br/>
CGDK.buffer의 기본적으로 구조체이므로 구조체 자체는 통채로 복사가 됩니다.
따라서 이렇게 복사를 하면
   ``` C#
   var buf = new CGDK.buffer(256);
         
   buf.Append<byte>(10);
   buf.Append<sbyte>(20);

   // 얇은 복사(Sallow Copy)
   var buf2 = buf;


   v1 = buf.Extract<byte>(); // 10이 됩니다.
   v2 = buf.Extract<sbyte>(); // 20이 됩니다.

   x1 = buf2.Extract<byte>(); // 10이 됩니다. Offset과 Count는 복사되어도 buffer 자체는 깊은 복사가 이루어지지 않아 공유됩니다.
   x2 = buf2.Extract<sbyte>(); // 20이 됩니다. 
   ```
   기본적인 버퍼의 복사는 얇은 복사를 수행하며 <br/>
   buffer참조와 Offset값과 Count 값은 복사가 되지만 버퍼 자체는 복사가 되지 않습니다. <br/>
   이런 형태의 얇은 복사는 아주 많이 사용됩니다.<br/>
   원본 CGDK.buffer의 Offset과 Count는 변경하지 않고 값을 읽고 싶을 경우 GetFront<T>()를 사용하는 것보다 이런 방식이 훨씬 많이 사용됩니다. <br/>
 <br/>
   만약에 깊은 복사를 원하시면 Clone()을 하십시요. <br/>

   ``` C#
   var buf = new CGDK.buffer(new byte[256]);
         
   buf.Append<byte>(10);
   buf.Append<sbyte>(20);

   // 버퍼 차제도 깊은 복사가 이루어집니다.(Deep Copy)
   var buf2 = buf.Clone();
   ```
<br/>
<br/>

## CGD.buffer 연산자<br/>
### 1. Offset을 주어 얇은 복사하기
-  '+ CGDK.Offset()'를 사용하면 Offset을 미리 주어 얇은 복사를 수행할 수 있습니다.<br>
이 Offset을 주는 얇은 복사는 매우 많이 사용합니다.<br>

   ``` C#
   // buf1을 Offset 8 만큼 주어서 얇은 복사를 한다.
   // 즉, buf2.Offset = buf1.Offset + 8, buf2.Count = buf1.Count - 8
   var buf2 = buf1 + new CGDK.Offset(8);
   ```

### 2. 크기를 조정해 얇은 복사하기
-  '+ CGDK.Size()'를 사용하면 Size를 늘리거나 줄여서 얇은 복사

   ``` C#
   // buf1을 Size를 8만큼 줄여서 얇은 복사를 한다.
   // 즉, buf2.Offset = buf1.Offset, buf2.Count = buf1.Count - 8
   var buf2 = buf1 - new CGDK.Size(8);
   ```

### 3. Offset을 대입한  얇은 복사하기
- '* CGDK.Offset()'으로 Offset을 앗사리 특정 값으로 대입해 얇은 복사를 수행할 수 있습니다.
   ``` C#
   // buf1을 Offset 8 만큼 주어서 얇은 복사를 한다.
   // 즉, buf2.Offset = 8, buf2.Count = buf1.Count
   var buf2 = buf1 ^ new CGDK.Offset(8);
   ```

### 4. Size를 대입한 얇은 복사하기
- '^ CGDK.Size()'로 Offset을 앗사리 특정 값으로 대입해 얇은 복사를 수행할 수 있습니다.
   ``` C#
   // buf1을 Offset 8 만큼 주어서 얇은 복사를 한다.
   // 즉, buf2.Offset = buf1.Offset, buf2.Count = 8
   var buf2 = buf1 ^ new CGDK.Size(8);
   ```

### 5. Offset과 Size 모두 변경해 얇은 복사하기
- '^ + tuple'로 Offset,Count 모두를 한번에 대입한 값으로 얇은 복사를 수행할 수 있습니다.
그냥 m_buffer만 복사하는 격입니다.
   ``` C#
   // buf1을 Offset 4, Count는 8로 설정한다.
   // 즉, buf2.Offset = 4, buf2.Count = 8
   var buf2 = buf1 ^ (new CGDK.Offset(4), new CGDK.Size(8));
   ```
<br/>

## 지원 가능<br/>
* C# .NET core<br/>
* C# .NET framework 버전을 설시하셔야 합니다. (다만 Roslyn과 Source Generator)는 지원하지 않습니다)<br/>
* unity 3D(c#) 지원<br/>

<br/>


> sangducks@gmail.com
> sangduck@cgcii.co.kr