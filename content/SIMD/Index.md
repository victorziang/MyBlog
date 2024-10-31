---
longform:
  format: scenes
  title: SIMD
  workflow: Default Workflow
  sceneFolder: /
  scenes: []
  ignoredFiles: []
---
有一些指令用于正确的类型转换，它们仅支持有符号32位整数通道：
- `_mm_cvtepi32_ps` 将4个32位整数通道转换为32位浮点数。
- `_mm_cvtepi32_pd` 将前两个整数转换为64位浮点数。
- `_mm256_cvtpd_epi32` 将4个双精度浮点数转换为4个整数，并将输出的高4个通道设置为零。
```
#include <emmintrin.h>

int main() {
    __m128i int_vector = _mm_setr_epi32(1, 2, 3, 4); // 初始化整数向量
    __m128 float_vector = _mm_cvtepi32_ps(int_vector); // 转换为浮点数向量
    return 0;
}

```
```
#include <emmintrin.h>

int main() {
    __m128i int_vector = _mm_setr_epi32(1, 2, 3, 4); // 初始化整数向量
    __m128d double_vector = _mm_cvtepi32_pd(int_vector); // 转换为双精度浮点数向量
    return 0;
}

```
```
#include <immintrin.h>

int main() {
    __m256d double_vector = _mm256_setr_pd(1.0, 2.0, 3.0, 4.0); // 初始化双精度浮点数向量
    __m256i int_vector = _mm256_cvtpd_epi32(double_vector); // 转换为整数向量
    return 0;
}

```
##### Initializing Vector Registers
###### Initializing with Zeros
```
\\初始化一个 `__m128` 类型的向量，将其所有四个单精度浮点数设置为零。
__m128 _mm_setzero_ps(void); 

\\初始化一个 `__m256i` 类型的向量，将其所有 256 位整数设置为零。
__m256i _mm256_setzero_si256(void);

```

<xmmintrin.h>：用于基本的 SSE 指令，处理 128 位数据，主要是单精度浮点数。
<immintrin.h>：用于更高级的 AVX 指令，支持 256 位及更高的数据宽度，处理浮点数和整数。
```
#include <emmintrin.h> // 包含 SSE 指令集的头文件

int main() {
    // 初始化一个 __m128 向量为零
    __m128 zero_vector = _mm_setzero_ps();
    return 0;
}

```
```
#include <immintrin.h> // 包含 AVX 指令集的头文件

int main() {
    // 初始化一个 __m256i 向量为零
    __m256i zero_vector = _mm256_setzero_si256();
    return 0;
}
```
###### Initializing with Values
1. _mm_set_something and _mm256_set_something
这些函数用于设置向量的元素值。

- **SSE**（128 位）：
    
    - **`_mm_set_ps`**：设置一个 128 位向量的四个单精度浮点数。
        
        `__m128 _mm_set_ps(float e3, float e2, float e1, float e0);`
        
        例如：
        
        `__m128 vec = _mm_set_ps(1.0f, 2.0f, 3.0f, 4.0f); // vec = {4.0, 3.0, 2.0, 1.0}`
        
- **AVX**（256 位）：
    
    - **`_mm256_set_ps`**：设置一个 256 位向量的八个单精度浮点数。
        
        `__m256 _mm256_set_ps(float e7, float e6, ..., float e0);`
        
        例如：
        
        `__m256 vec = _mm256_set_ps(1.0f, 2.0f, 3.0f, 4.0f, 5.0f, 6.0f, 7.0f, 8.0f); // vec = {8.0, 7.0, 6.0, 5.0, 4.0, 3.0, 2.0, 1.0}`
2. _mm_set1_something and _mm256_set1_something
这些函数用于将一个标量值复制到向量的所有元素中。

- **SSE**（128 位）：
    
    - **`_mm_set1_ps`**：将一个单精度浮点数复制到 128 位向量的所有元素。
        
        `__m128 _mm_set1_ps(float a);`
        
        例如：
        
        `__m128 vec = _mm_set1_ps(3.0f); // vec = {3.0, 3.0, 3.0, 3.0}`
        
- **AVX**（256 位）：
    
    - **`_mm256_set1_ps`**：将一个单精度浮点数复制到 256 位向量的所有元素。
        
        `__m256 _mm256_set1_ps(float a);`
        
        例如：
        
        `__m256 vec = _mm256_set1_ps(3.0f); // vec = {3.0, 3.0, 3.0, 3.0, 3.0, 3.0, 3.0, 3.0}`
3. _mm_insert_something
这些函数用于将一个标量值插入到向量的指定位置。

- **SSE**（128 位）：
    
    - **`_mm_insert_ps`**：将一个单精度浮点数插入到 128 位向量的指定位置。
        
        `__m128 _mm_insert_ps(__m128 a, __m128 b, const int imm8);`
        
        例如：
        
        `__m128 vec = _mm_set_ps(1.0f, 2.0f, 3.0f, 4.0f); vec = _mm_insert_ps(vec, _mm_set_ps(0.0f, 5.0f, 0.0f, 0.0f), 0x10); // 将 5.0 插入到 vec 的第二个位置`
        
- **AVX**（256 位）：
    
    - AVX 没有直接对应的 `_mm256_insert_ps` 函数，但可以通过其他方式（如使用 `_mm256_blend_ps`）来完成类似的功能。
`_mm256_blend_ps` 是 AVX 指令集中的一个函数，用于在两个 256 位的单精度浮点向量之间进行条件混合。具体来说，它根据指定的掩码选择来自两个向量的元素。
```
__m256 _mm256_blend_ps(__m256 a, __m256 b, const int imm8);
### 参数

- **`a`**：第一个输入向量（256 位）。
- **`b`**：第二个输入向量（256 位）。
- **`imm8`**：一个 8 位的掩码，决定了从哪个向量取哪些元素。每个比特位对应于一个元素：
    - 如果某个比特为 0，则取自向量 `a` 的相应元素。
    - 如果某个比特为 1，则取自向量 `b` 的相应元素。比特位的顺序是从低到高，分别对应向量的元素 0 到 7。
```
下面是一个简单的示例，演示如何使用 `_mm256_blend_ps`：
```
#include <immintrin.h>
#include <stdio.h>

int main() {
    // 创建两个 256 位的浮点向量
    __m256 a = _mm256_set_ps(1.0f, 2.0f, 3.0f, 4.0f, 5.0f, 6.0f, 7.0f, 8.0f); // {8.0, 7.0, 6.0, 5.0, 4.0, 3.0, 2.0, 1.0}
    __m256 b = _mm256_set_ps(9.0f, 10.0f, 11.0f, 12.0f, 13.0f, 14.0f, 15.0f, 16.0f); // {16.0, 15.0, 14.0, 13.0, 12.0, 11.0, 10.0, 9.0}

    // 使用掩码 0b10101010，选择 a 的偶数索引元素和 b 的奇数索引元素
    __m256 result = _mm256_blend_ps(a, b, 0b10101010);

    // 打印结果
    float output[8];
    _mm256_storeu_ps(output, result);
    for (int i = 0; i < 8; i++) {
        printf("%f ", output[i]);
    }
    // 输出: 9.0 2.0 11.0 4.0 13.0 6.0 15.0 8.0

    return 0;
}

```
**对于所有 `_mm[256]_set_something` 内在函数，Intel 在顺序上有些问题。要创建一个具有整数值 `[1, 2, 3, 4]` 的寄存器，需写作 `_mm_set_epi32(4, 3, 2, 1)` 或 `_mm_setr_epi32(1, 2, 3, 4)`。**

###### Bitwise Instructions
按位取反:
```
__m128i bitwiseNot(__m128i x) {
    const __m128i zero = _mm_setzero_si128();  //初始化一个全为零的向量
    const __m128i one = _mm_cmpeq_epi32(zero, zero);//比较两个向量 `zero` 和 `zero`是否相等，相等返回1,不相等返回0
    return _mm_xor_si128(x, one);//`_mm_xor_si128` 函数对输入向量 `x` 和 `one` 进行按位异或操作。由于异或的性质，任意数与 1 异或会得到其按位取反的结果。因此，这里返回的结果是 `x` 的按位取反。
}

```
`_mm_min_ss`  `_mm_max_ss`  /`_mm_min_sd`  `_mm_max_sd` 单/双精度浮点数最小最大值
`_mm_rcp_ps` /`_mm_rsqrt_ps`    计算单精度浮点数的倒数/平方根的倒数。
`_mm_hadd_ps`接受两个寄存器 `[a, b, c, d]` 和 `[e, f, g, h]` 返回 `[a+b, c+d, e+f, g+h]`
`_mm_addsub_ps` 接受两个寄存器 `[a, b, c, d]` 和 `[e, f, g, h]` 返回 `[a-e, b+f, c-g, d+h]``_mm_addsub_pd` 对双精度通道做类似操作。适用于复数乘法等。

[https://stackoverflow.com/questions/16988199/how-to-choose-avx-compare-predicate-variants/64191351#64191351]
