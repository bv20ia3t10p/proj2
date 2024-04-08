---
marp: true
---

### Lỗi và xử lý lỗi
1. Giúp xử các trường hợp không mong muốn
2. Dùng try, catch và finally
3. Ngoại lệ có thể được tạo bằng throw
4. CLR sẽ tìm từ nơi throw exception đến nơi có catch hợp lệ nhất, nếu không sẽ báo cho người dùng.
---
### Ví dụ lỗi và xử lý lỗi
```cs
public class ExceptionTest
{
    static double SafeDivision(double x, double y)
    {
        if (y == 0)
            throw new DivideByZeroException();
        return x / y;
    }

    public static void Main()
    {
        // Input for test purposes. Change the values to see
        // exception handling behavior.
        double a = 98, b = 0;
        double result;

        try
        {
            result = SafeDivision(a, b);
            Console.WriteLine("{0} divided by {1} = {2}", a, b, result);
        }
        catch (DivideByZeroException)
        {
            Console.WriteLine("Attempted divide by zero.");
        }
    }
}
```
---
### Ngoại lệ
1. Là các lớp kế thừa từ System.Exception
2. Dùng lệnh try để bọc khối lệnh
3. Lệnh catch dùng để định nghĩa khối xử lý ngoại lệ
4. Không nên catch nếu không thế xử lý, hoặc có thể throw ra ngoài
5. Có thể truy cập thông tin lỗi thông qua biến Exception ở catch
6. finally sẽ chạy bất kể exception có được throw hay không
 ---
 ### Sử dụng exception
 1. Là các lớp kế thừa từ Exception
 2. Được tạo và truyền đi khi có throw keyword
 ```cs
 class CustomException : Exception
{
    public CustomException(string message)
    {
    }
}
private static void TestThrow()
{
    throw new CustomException("Custom exception in TestThrow()");
}
 ```
 ---
 ### Bắt exception
 1. Khi một exception được throw compiler sẽ tìm từ nơi throw để có được khối catch
 2. Catch block thường chỉ định cụ thể tên Exception, nếu Exception cùng lớp với kiểu của khối catch thì khối catch này sẽ đảm nhiệm xử lý lỗi
 3. Một khối lệnh try có thể được đi kèm với nhiều catch, khối catch thỏa điều kiện đầu tiên sẽ được thực thi và bỏ qua các khối khác.
```cs
try
{
    TestThrow();
}
catch (CustomException ex)
{
    System.Console.WriteLine(ex.ToString());
}
```
---
### Catch
1. Có thể chỉ định cụ thể lớp Exception
2. Không nên sử dụng lớp base Exception để filter
3. Có thể chỉ định một boolean expression bên cạnh Exception filter
4. Được thực thi khi có khối 
---
### Ví dụ catch
```cs
public class ExceptionFilter
{
    public static void Main()
    {
        try
        {
            string? s = null;
            Console.WriteLine(s.Length);
        }
        catch (Exception e) when (LogException(e))
        {
        }
        Console.WriteLine("Exception must have been handled");
    }

    private static bool LogException(Exception e)
    {
        Console.WriteLine($"\tIn the log routine. Caught {e.GetType()}");
        Console.WriteLine($"\tMessage: {e.Message}");
        return false;
    }
}
```
---
### Finally
1. Dùng để "dọn dẹp" các hành động được thực hiện trong khối lệnh try
2. Luôn chạy mặc dù exception có được catch hay không
3. Thường được dùng để đóng file, db connection, ...
```cs
FileStream? file = null;
FileInfo fileinfo = new System.IO.FileInfo("./file.txt");
try
{
    file = fileinfo.OpenWrite();
    file.WriteByte(0xF);
}
finally
{
    // Check for null because OpenWrite might have failed.
    file?.Close();
}
```
---
### Delegate
1. Tương tự với function pointer của js và Cpp
2. Cho phép hàm được truyền vào dưới dạng tham số
3. Dùng để định nghĩa các hàm callback
4. Có thể được nối với nhau thành một chuỗi
5. Thường được viết bằng inline function
---
### Sử dụng Delegate
Định nghĩa delegate
```cs
public delegate void Callback(string message);
```
Delegate thường được dựng bởi cung cấp tên của hàm delegate sẽ bọc hoặc dùng lambda expression
```cs
// Create a method for a delegate.
public static void DelegateMethod(string message)
{
    Console.WriteLine(message);
}
// Instantiate the delegate.
Callback handler = DelegateMethod;

// Call the delegate.
handler("Hello World");
```
---
### Kết hợp/ Multicast Delegate
Sử dụng toán tử +=, -=, hoặc + khi assignment để thêm/ xóa các callback fn ra khỏi danh sách gọi của delegate
```cs
using System;
delegate void CustomCallback(string s);

class TestClass
{
    // Define two methods that have the same signature as CustomCallback.
    static void Hello(string s)
    {
        Console.WriteLine($"  Hello, {s}!");
    }

    static void Goodbye(string s)
    {
        Console.WriteLine($"  Goodbye, {s}!");
    }

    static void Main()
    {
        CustomCallback hiDel, byeDel, multiDel, multiMinusHiDel;
        hiDel = Hello;
        byeDel = Goodbye;
        multiDel = hiDel + byeDel;
        multiMinusHiDel = multiDel - hiDel;

        Console.WriteLine("Invoking delegate hiDel:"); hiDel("A");
        Console.WriteLine("Invoking delegate byeDel:"); byeDel("B");
        Console.WriteLine("Invoking delegate multiDel:"); multiDel("C");
        Console.WriteLine("Invoking delegate multiMinusHiDel:"); multiMinusHiDel("D");
    }
}
```
---
### Generic
1. Được dùng để tượng trưng cho một kiểu
2. Là blueprint cho một kiểu
3. Được xác định tại compile time
4. Có thể dùng where để ràng buộc kiểu của generic
```cs
GenericList<float> list1 = new GenericList<float>();
GenericList<ExampleClass> list2 = new GenericList<ExampleClass>();
GenericList<ExampleStruct> list3 = new GenericList<ExampleStruct>();
```
---
### Generic functions
Quy định kiểu của tham số truyền vào/ kiểu trả về
```cs
static void Swap<T>(ref T lhs, ref T rhs)
{
    T temp;
    temp = lhs;
    lhs = rhs;
    rhs = temp;
}
```
```cs
public static void TestSwap()
{
    int a = 1;
    int b = 2;

    Swap<int>(ref a, ref b);
    System.Console.WriteLine(a + " " + b);
}
```
---
### Generic Delegate
Linh động tham số và kiểu trả về của các hàm delegate
```cs
public delegate void Del<T>(T item);
public static void Notify(int i) { }

Del<int> m1 = new Del<int>(Notify);
```