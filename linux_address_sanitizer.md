
https://github.com/google/sanitizers

https://github.com/google/sanitizers/wiki/AddressSanitizer

* ֧�ְ汾  Clang (3.3+) and GCC (4.8+). ���� gcc6 ���ã��������������°汾��

* ģʽ 1����ĳ��������һ�������ƣ���ִ�г��򣩣�������򵥵� `g++ 1.c` ,
~~~
��ô���ڱ����ʱ��� `-fsanitize=address`, ����Ϊ `g++ -fsanitize=address  1.c`.
~~~

* ģʽ 2����������ģʽ�� �����ƣ���ִ�г���+��̬�� .so ��������Ҫ������̬�� so �ļ���
~~~
��ô 

  a. �ڱ��� so �ļ�ʱ�������²���
  
  `CXXFLAGS+= -O1 -g -DNDEBUG  -fsanitize=address`
  gcc-6: `-fsanitize-recover=address` ��λ�� 1 ���������Բ�ֹͣ���У��������е�ǰ����
  
	
  b. ���� .o �ļ�Ϊ .so �ļ���ʱ�򣬼� 
  
  `-g -fno-omit-frame-pointer -fsanitize=address -fsanitize-recover=address -lasan`
  
   ���Ӻ�Ҫʹ�� `ldd -r [.so path]` �鿴���ɵ� so �ļ��Ƿ��з���δ���ӣ������asan�ģ�Ҫ�ص� a�� ���±��룬��ǰ׺
	 
   `export  LD_LIBRARY_PATH=/usr/lib/clang/3.8.0/lib/linux & make -j8`
	 
  c. ����ʱ��ǰ׺ 
  `SAN_OPTIONS=halt_on_error=0` �׳������ֹͣ����
  
  gcc-5:`LD_PRELOAD=/usr/lib/gcc/x86_64-linux-gnu/5/libasan.so` ��
  gcc-6:`LD_PRELOAD=/usr/lib/gcc/x86_64-linux-gnu/6/libasan.so` / `LD_PRELOAD=/usr/lib/gcc/x86_64-linux-gnu/6.2.0/libasan.so` ��
  
  ��� so ��·��Ҫ�Լ��ҡ�
    
	�����쳣ʱ�������������쳣�Ĵ����ļ����кţ�������ʱ����������ǰ׺ 
	
    `ASAN_SYMBOLIZER_PATH=/usr/bin/llvm-symbolizer-3.8 ASAN_OPTIONS=symbolize=1`
~~~
* ȱ��
~~~c++
�޷����ջ�ռ�Խ��

int x[4];
x[4]=9;

���ϴ���  gcc-5(5.4)��ⲻ��, gcc-6 ���Լ�⵽��
~~~