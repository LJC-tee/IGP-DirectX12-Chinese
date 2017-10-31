# <element id = "6"> Chapter 6 DRAWING IN DIRECT3D </element>

��֮ǰ���½��У�������Ҫ����������Ⱦ�ܵ��е�һЩ�����һЩ��ѧ֪ʶ��
�ڱ����У����Ǿ�תΪ��Ҫ����`Direct3D API`���Ӷ���������Ⱦ�ܵ�����д������ɫ����������ɫ�����Լ��ύͼ�ε���Ⱦ�ܵ����һ�������
�ڱ��½�����ʱ�����Ǿ��ܹ��ɹ�����һ���������ˡ�

**Ŀ��:**
- �˽�һЩ���ڶ��壬�洢����ͼ�ε�`Direct3D API`��
- �˽�ѧϰ��α�д�����Ķ�����ɫ����������ɫ�����롣
- �˽����ʹ�ùܵ�״̬��������Ⱦ�ܵ���
- �˽���δ���һ�������岢�ҽ���󶨵���Ⱦ�ܵ���ȥ��������Ϥ`Root Signature`(���ҽ�������������Ҫ�����ڶ�������ɫ��ʹ����Щ��Դ)��

## <element id = "6.1"> 6.1 VERTICES AND INPUT LAYOUTS </element>

�ع�5.5.1����`Direct3D`�У������������λ�����������Ը���һЩ�������ݡ�
Ϊ�˴���һ���Զ���Ķ����ʽ��������Ҫ����һ���ṹ������������Ҫ�����㸽�ӵ����ݡ�
����;ٳ�������ͬ�Ķ����ʽ�����ӣ�����һ��������λ�ú���ɫ���ݣ�����һ��������λ�ã����������Լ����������������ݡ�

```C++

struct Vertex1
{
    Float3 Pos;
    Float4 Color;
};

struct Vertex2
{
    Float3 Pos;
    Float3 Normal;
    Float2 Tex0;
    Float2 Tex1;
};

```

������Ȼ�����˶����ʽ����������ͬ����Ҫ����`Direct3D`���ǵĶ����ʽ����Ȼ�Ļ�`Direct3D`��û��֪�����Ǹ����㸽������Щ��������ݡ�
����ʹ��`D3D12_INPUT_LAYOUT_DESC`��������¡�

```C++

struct D3D12_INPUT_LAYOUT_DESC 
{
    const D3D12_INPUT_ELEMENT_DESC *pInputElementDescs;
    UINT NumElements;
};

```

- `pInputElementDescs`: һ�����飬����`Direct3D`���ǵĶ��㸽�ӵ�������Ϣ��
- `NumElements`: ����Ĵ�С��

`pInputElementDescs`�����ÿһ��Ԫ�ض��൱�ڶ����ʽ��һ������������Ϣ��
���������ǵ�һ�������ʽ������������������Ϣ�Ļ�����ô�������Ĵ�С����Ҫ��������

```C++
struct D3D12_INPUT_ELEMENT_DESC
{
    LPCSTR SemanticName;
    UINT SemanticIndex;
    DXGI_FORMAT Format;
    UINT InputSlot;
    UINT AlignedByteOffset;
    D3D12_INPUT_CLASSIFICATION InputSlotClass;
    UINT InstanceDataStepRate;
};
```

- `SemanticName`: һ��������ϵԪ�ص��ַ���������ֵ�����ںϷ��ķ�Χ�ڡ�����ʹ��������������е�Ԫ��ӳ�䵽��ɫ��������������ȥ���μ�ͼƬ[6.1](#Image6.1)��
<img src="Images/6.1.png" id = "Image6.1"> </img>
- `SemanticIndex`: ����ֵ��������Բμ�ͼƬ[6.1](#Image6.1)������һ��������ܻ��в�ֹһ���������꣬���Ǳ���������Щ�������꣬������Ǿͼ���������ֵ������һ����������Ķ���������ꡣ���һ��`semanticName`û�м��������Ļ���Ĭ��Ϊ����ֵΪ0������`POSITION`����`POSITION0`��
- `Format`: ���ӵ�������Ϣ�ĸ�ʽ��������`DXGI_FORMAT`��
- `InputSlot`: ָ�����Ԫ�ش��ĸ�����ڽ��룬`Direct3D`֧��16�������(0-15)���붥�����ݡ�����������˵������ֻʹ��0����ڡ�
- `AlignedByteOffset`: �ڴ�ƫ��������λ���ֽڡ��Ӷ���ṹ�Ŀ��˵����Ԫ�صĿ��˵��ֽڴ�С��������һ�����ӡ�
    ```C++
    struct Vertex
    {
        Float3 Pos; // 0-byte offset
        Float3 Normal; // 12-byte offset
        Float2 Tex0; // 24-byte offset
        Float2 Tex1; // 32-byte offset
    };
    ```
- `InputSlotClass`: ����Ĭ��ʹ��`D3D12_INPUT_PER_VERTEX_DATA`�������һ������������`Instancing`�����ġ�

�������Ǹ�һ������: 

```C++
struct Vertex
{
    Float3 Pos; // 0-byte offset
    Float3 Normal; // 12-byte offset
    Float2 Tex0; // 24-byte offset
    Float2 Tex1; // 32-byte offset
};

D3D12_INPUT_ELEMENT_DESC desc [] = 
{
    	{��POSITION��, 0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 0,  D3D12_INPUT_PER_VERTEX_DATA, 0},
        {��NORMAL��,   0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 12, D3D12_INPUT_PER_VERTEX_DATA, 0},
        {��TEXCOORD��, 0, DXGI_FORMAT_R32G32_FLOAT,    0, 24, D3D12_INPUT_PER_VERTEX_DATA, 0},
        {��TEXCOORD��, 1, DXGI_FORMAT_R32G32_FLOAT,    0, 32, D3D12_INPUT_PER_VERTEX_DATA, 0}     
};
```
 
 ## <element id = "6.2"> 6.2 VERTEX BUFFERS </element>

 Ϊ���ܹ���`GPU`����һ�鶥���������Ϣ��������Ҫ��������Ϣ���뵽`GPU`��Դ��ȥ(**ID3D12Resource**)�����ǳ�֮Ϊ���塣
 ���ǽ��洢�������ݵĻ����֮Ϊ���㻺��(`Vertex Buffer`)��
 ����������һЩ����ֻ��һά��������û��������ϸ(`MipMaps`)��������(`filters`)�����ز���(`multisampling`)��Щ������
 ������ʲôʱ���������Ҫ��`GPU`�ṩһ��������Ϣ���綥��������Ϣ�����Ƕ���ʹ�û�����ʵ�֡�

 ������֮ǰ����ͨ�����`D3D12_RESOURCE_DESC`����������һ��`ID3D12Resource`��
 �������Ҳ��ͨ�������ķ�ʽ������һ�����壬�����`D3D12_RESOURCE_DESC`�ṹ����������Ҫ�����Ļ�������ԣ�Ȼ��ʹ��`ID3D12Device::CreateCommittedResource`�������塣

 ��`d3dx12.h`���ṩ��һ����������`CD3D12_RESOURCE_DESC`��������Դ���������ȥ�μ�`d3dx12.h`��

 **ע��������`Direct3D 12`�в�û�ж���һ���������������ʾ�����Դ��һ�����������һ������(`Direct3D 11`������������)��
 ��������ڴ�����Դ��ʱ����Ҫͨ��`	D3D12_RESOURCE_DESC::D3D12_RESOURCE_DIMENSION`��ָ����Դ�����͡�**

 ���ھ�̬��ͼ��(��ÿһ֡��ͨ������ı�)�����ǻὫ���Ķ��㻺�����Ĭ�϶���(**Default Heap**)�Ա������ŵ����ܣ�ͨ���󲿷���Ϸ�е�ͼ�ζ������Ĭ�϶��У������������������Σ�����ȡ�
 ��Ϊ�����ڴ��������Ķ��㻺���`GPU`ֻ���ȡ����Ķ�����Ϣ���������������������������飬��˽������Ĭ�϶���������õġ�
 Ȼ������`GPU`�����ܽ�����д�뵽����Ĭ�϶��������Դ�����Ǹ���ν���ʼ�Ķ������ݷŵ�������ȥ��

 Ϊ��ʵ�������������Ҫ����һ���ϴ�������Դ(`Upload Buffer`)�����ᱻ�����ϴ�����(**Upload Heap**)��
 �ع�4.3.8�½ڣ�������Ҫ�����ݴ��ڴ濽�����Դ���ȥ��ʱ�������ύ��һ����Դ���ϴ����С�
 �����Ǵ�����һ���ϴ���������ǽ��������ݿ������ϴ������У�Ȼ�����Ǵ��ϴ������п����������ݵ����ǵĶ��㻺����ȥ��

 ֮���������һ�����ӣ���������Լ�ȥ�����롣��ֻ��������Ҫʹ�õĽṹ�����ͽ����¡�

 ```C++
 struct D3D12_SUBRESOURCE_DATA
 {
    const void *pData;
    LONG_PTR RowPitch;
    LONG_PTR SlicePitch;
 };
 ```

 - `pData`: �������Ԫ��ָ�롣
 - `RowPitch`: ���ڻ�����˵���������ǿ��������ݵĴ�С����λ�ֽڡ�
 - `SlicePitch`: ���ڻ�����˵���������ǿ��������ݵĴ�С����λ�ֽڡ�

ע����ǣ����ڶ��㻺����˵�����Ǵ��������������ǲ���Ҫ����������������еġ�

```C++
struct D3D12_VERTEX_BUFFER_VIEW
{
    D3D12_GPU_VIRTUAL_ADDRESS BufferLocation;
    UINT SizeInBytes;
    UINT StrideInBytes;
};
```

- `BufferLocation`: ������Ҫʹ�õĶ��㻺��������ַ�����ǿ���ʹ��`ID3D12Resource::GetGPUVirtualAddress`����ȡ��ַ��
- `SizeInBytes`: ����������ֻ������ֻʹ�û����е�һ�������ݣ������������������������Ҫʹ�õĻ����С����λ�ֽڣ���`BufferLocation`λ�ÿ�ʼ����ƫ�ơ�
- `StrideInBytes`: ÿ������Ԫ�صĴ�С����λ�ֽڡ�

�����Ǵ����껺��ͻ���������������ǿ��Խ����ǰ󶨵���Ⱦ�ܵ�������ڣ�Ȼ��������װ��׶ν����㻺�������ȥ��


```C++
    void ID3D12GraphicsCommandList::IASetVertexBuffers(
        UINT StartSlot,
        UINT NumBuffers,
        const D3D12_VERTEX_BUFFER_VIEW *pViews);
```

- `StartSlot`: �󶨵Ķ��㻺�������ڵ���ʼλ�ã��ܹ���16������Χ��0-15֮�䡣
- `NumBuffers`: ����Ҫ�󶨵Ķ��㻺��ĸ��������Ǽ������ǰ�n�����㻺�壬����ڵ���ʼλ����k����ô��i�����㻺�������ھ���`k + i - 1`��
- `pViews`: ����Ҫ�󶨵Ķ��㻺����������������Ԫ�صĵ�ַ��

**������˵����DX12���֧�ֵ�����ڸ�����32��**

����Ҫ֧�ֶ�����㻺�������һ��������������ݣ����������Ƶľ��е㸴���ˡ�
��������������ֻʹ��һ������ڡ����½�������ϰ�����ǻ�ʹ�õ���������ڡ�

ֻ�е����Ǹı��������ڰ󶨵Ķ��㻺���ʱ��ԭ���Ķ��㻺��Ż�ȡ���󶨡�
���������Ȼֻʹ��һ������ڵ���������Ȼ����ʹ�ö�����㻺�塣����������

```C++
    D3D12_VERTEX_BUFFER_VIEW_DESC BufferView1;
    D3D12_VERTEX_BUFFER_VIEW_DESC BufferView2;

    /*Create Vertex Buffer Views*/

    commandList->IASetVertexBuffers(0, 1, &BufferView1);

    /*Draw by using VertexBuffer1*/

    commandList->IASetVertexBuffers(0, 1, &BufferView2);

    /*Draw by using VertexBuffer2*/
```

��һ�����㻺�嵽����ڲ����������ǻ�����������壬����ֻ��׼�����ö������뵽��Ⱦ�ܵ��ж��ѡ�
���������ǻ���Ҫʹ�û��ƺ�����������Щ���㡣

```C++
    void ID3D12CommandList::DrawInstanced(
        UINT VertexCountPerInstance,
        UINT InstanceCount,
        UINT StartVertexLocation,
        UINT StartInstanceLocation);
```

- `VertexCountPerInstance`: ������Ҫ���ƵĶ������(����ÿ��ʵ����˵)��
- `InstanceCount`: ����Ҫ���Ƶ�ʵ��������������������Ϊ1��
- `StartVertexLocation`: ָ���Ӷ��㻺���еĵڼ������㿪ʼ���ơ�
- `StartInstanceLocation`: ������������Ϊ0��

`VertexCountPerInstance`��`StartVertexLocation`һ�����������Ҫ���ƶ��㻺���е��ĸ���Χ��
ͼƬ[6.2](#Image6.2)���������ӡ�

<img src="Images/6.2.png" id = "Image6.2"> </img>

`StartVertexLocation`ָ��������Ҫ���Ƶ�һ�������ڶ��㻺���е�λ�ã�`VertexCountPerInstance`ָ��������Ҫ���ƵĶ��������

`DrawInstanced`��û��������ָ�����ǻ��Ƶ�ʱ��ʹ�õ�ͼԪ�����͡�
���������Ҫʹ��`ID3D12GraphicsCommandList::IASetPrimitiveTopology`ȥ���á�

## <element id = "6.3"> 6.3 INDICES AND INDEX BUFFERS</element>

�Ͷ������ƣ�Ϊ���ܹ���`GPU`�ܹ����ʵ��������ݣ�����ͬ����Ҫ���������ݷŵ�������ȥ��
���ǳ�֮Ϊ�������塣��������Ĵ�����ʽ�Ͷ��㻺����һ���ģ��������Ͳ��������ˡ�

����ͬ����Ҫ����������󶨵���Ⱦ�ܵ���ȥ���������ҲҪΪ�������崴�������������ҺͶ��㻺��һ�������ǲ���Ҫʹ�õ��������ѡ�

```C++
struct D3D12_INDEX_BUFFER_VIEW
{
    D3D12_GPU_VIRTUAL_ADDRESS BufferLocation;
    UINT SizeInBytes;
    DXGI_FORMAT Format; 
};
```

- `BufferLocation`: �Ͷ��㻺���һ����
- `SizeInBytes`: �Ͷ��㻺���һ����
- `Format`: һ������ռ�ݵ��ֽڴ�С����������Ϊ`DXGI_FORMAT_R16_UINT`����`DXGI_FORMAT_R32_UINT`��

�Ͷ��㻺��һ�����Լ�������`Direct3D`��Դ���������Ҫʹ�����ǵĻ���һ�㶼��Ҫ����󶨵���Ⱦ�ܵ���ȥ��
��������ͬ��Ҳ��Ҫ����������󶨵��Ͷ��㻺��һ���Ľ׶�(ʹ��`ID3D12CommandList::SetIndexBuffer`)��������װ��׶Ρ�


����Ĵ�����һ�����ӣ������ȥ�Լ�������

�������Ҫʹ����������Ļ������ǾͲ��ܹ�ʹ��`DrawInstanced`������ͼ���ˣ�������ʹ��`DrawIndexedInstanced`�����ơ�

```C++
    ID3D12GraphicsCommandList::DrawIndexedInstanced(
        UINT IndexCountPerInstance,
        UINT InstanceCount,
        UINT StartIndexLocation,
        INT BaseVertexLocation,
        UINT StartInstanceLocation);
```

- `IndexCountPerInstance`: ���ǻ��Ƶ�ʱ��ʹ�õ�����������
- `InstanceCount`: ʵ��������������������Ϊ1��
- `StartIndexLocation`: ָ���Ӷ��㻺���е��ĸ�����λ�ÿ�ʼ���ơ�
- `BaseVertexLocation`: ָ�����ǻ��Ƶ�ʱ��ʹ�õĵ�һ�������ڶ��㻺���е�λ�ã����ڶ��㻺�����������֮ǰ�Ķ������ǲ���ʹ�ã����Ǵ�������㿪ʼ���±�š�
- `StartInstanceLocation`: ������������Ϊ0��

����������һ�����壬һ�������壬һ��Բ���塣
����ÿ�����嶼�����Լ��Ķ��㻺����������塣
Ȼ�����ǽ�ÿ���������������Ͷ��㻺��������������ƴ���������һ�����������һ�����㻺�壬���ҽ���ȫ�ֻ���ɣ����Բμ�ͼƬ[6.3](#Image6.3)(�����㻺�����������ƴ����Ҳ��һЩ���õĵط�����������Ҫ�ı�����һ������Ķ��㻺��������������ʱ�����ܿ�����ȷֿ���˵Ҫ�࣬�����ڴ�����������˵����������Ż���ȷֿ���˵��)��
�ڽ�����ƴ����������ô�ͻ����һ�����⣬�������������д洢������ֵ�������ԭ�����Ǹ����㻺����˵�ģ��������¼�Ķ�����Ҳ�������ԭ���Ķ��㻺����˵�ģ������������ｫ���㻺��ƴ�������󣬿϶���һЩ����ı���Ǹı���ˡ�
���������Ҫ���¼����������塣

<img src="Images/6.3.png" id = "Image6.3"> </img>

ֵ�����ҵ������ǵĶ��㻺����������岢���Ƿ�ɢ�ģ�һ������Ķ��㻺������������嶼��ȫ�ֻ����е�һ�λ��塣
��������ܹ������׵ľ�֪��һ���������Ķ��㻺��ķ�Χ����������ķ�Χ��
��������������������ֵ�������ԭ���Ķ��㻺����˵�ģ��������Ҫ���¼�����������Ļ���Ȼ�������㣬����������Ǽ���������������Ӧ�Ķ��㻺��ĵ�һ������ı����ȫ�ֻ����еı��Ϊ0�Ļ�����ô�͸պÿ��Զ��ϡ�
������Ǿ�����`BaseVertexLocation`��������ȫ�ֻ����е�һ������ı����ʱ����Ϊ0��

```C++
    commandList->DrawIndexedInstanced(numSphereIndices, 1, 0, 0, 0);
    
    commandList->DrawIndexedInstanced(numBoxIndices, 1, 
        firstBoxIndex, firstBoxVertexPos, 0);

    commandList->DrawIndexedInstanced(numCylIndices, 1, 
        firstCylIndex, firstCylVertexPos, 0);
```



## <element id = "6.4"> EXAMPLE VERTEX SHADER </element>

```hlsl

cbuffer PerObject : register(b0)
{
    float4x4 gWorldViewProj;
};

void VsMain(float3 pos : POSITION,
    float4 inputColor : COLOR, 
    out float4 posH : SV_POSITION,
    out float4 outColor : COLOR)
{
    posH = mul(float4(pos, 1.0f), gWorldViewProj);
    
    outColor = inputColor;
}

```

����ʹ��**HLSL**(**High Level Shading Language**)��������д��ɫ��������**C++** �ǳ����ƣ���˷ǳ����׾��ܹ�ѧ�ᡣ
���ǻ����Ķ������л���ѧϰ����HLSL��ɫ����һЩ����Ӷ������ܹ��Լ���ʵ��һЩ�򵥵���ʾ����
����ͨ����ʹ��`.hlsl`��ʽ����ʾ����ļ���һ����ɫ�����롣

�������У�������ɫ�����ǽ���`VSMain`�ĺ����������ڶ�����ɫ���׶ξͻ��������������
����Ը���Ķ�����ɫ��ȡ�κκϷ��ĺ�����������ֻ��Ҫ�ڱ����ʱ��ָ����ڵ㺯���ͺ��ˡ�
���ǿ��Կ��������еĶ�����ɫ����4��������ǰ������������Ĳ�������������������Ĳ���(ʹ��**out**��־)��
��HLSL����û���κ����ú�ָ��ģ�����Ҫ��һ���������ض�������Ļ���Ҫôʹ�ýṹ��Ҫô��ʹ��**out**�ؼ���������һ�����������������
����Ҫע�⣬��**HLSL**�����еĺ�������`inline`�ġ�

���ǿ���֪������ǰ����������Ĳ��������ǻ��Ƶ�ʱ��ʹ�õĶ����ʽ�����Ӧ�ģ��ڲ��������`POSITION`,`COLOR`���������������е�����ӳ�䵽������ɫ���Ĳ�����ȥ��

<img src="Images/6.4.png" id = "Image6.4"> </img>

���������������������`SV_POSITION`,`COLOR`��������������׶����������ӳ�䵽��һ���׶�(���缸����ɫ����������ɫ��)�����������ȥ��
��Ҫע�����`SV_POSITION`����΢��һ�������(**SV**��ʾ����˼��**System Value**)��
����ʾ���ǵĶ�������βü��ռ��λ�ã����Ǳ���ʹ�������־����Ķ���λ�ö�����`POSITION`��
��Ϊ`GPU`����Ҫ֪����Щ�����λ�õģ��Ӷ����ܹ�����һЩ��Ҫ����λ����Ϣ�Ĳ���������ü�����Ȳ����Լ���դ����
����ϵͳֵ��������Ҫʹ��ָ���ı�־������ı�־�������ǿ�������ȡ�ģ�ֻ��Ҫ�㵽ʱ���ܹ���Ӧ�Ͼͺ��ˡ�

��һ�д����У��������ľ���ʹ��һ�����󽫶��������ģ�Ϳռ�ת������βü��ռ䡣

```hlsl
    posH = mul(float4(pos, 1.0f), gWorldViewProj);
```

`float4`������������Ҳ�ǹ��캯����`mul`���ж�����أ�֧�ָ������͵ĳ˷���

Ȼ��һ�д�������ֻ�ǽ��������ɫ��Ϊ�������ɫ���ѡ�

```hlsl
    outColor = inputColor;
```

����ͬ��Ҳ��������ɫ���м���ṹ�����䵱���������Ĳ�����ֻҪ�ܹ������Ӧ�Ϳ����ˡ�

```hlsl
cbuffer PerObject : register(b0)
{
    float4x4 gWorldViewProj;
};

struct InputVertex
{
    float3 pos : POSITION;
    float4 inputColor : COLOR;
};

struct OutputVertex
{
    float4 posH : SV_POSITION;
    float4 outColor : COLOR;
};

OutputVertex VsMain(InputVertex input)
{
    OutputVertex output;

    output.posH = mul(float4(input.pos, 1.0f), gWorldViewProj);
    
    output.outColor = input.inputColor;

    return output;
}
```

�������û��ʹ�ü�����ɫ���Ļ������Ǿͱ����ڶ�����ɫ���������������βü��ռ��е�λ�ã���������һ��������`SV_POSITION`��־��
���������ʹ�ü�����ɫ���Ļ�����ô���ǾͿ����ڼ�����ɫ���������

͸�ӳ���������Ҫ����ȥ����ͨ������Ӳ��������

## <element id = "6.5"> 6.5 EXAMPLE PIXEL SHADER </element>

����֮ǰ��**5.10.3**�н����ڹ�դ����ʱ��ÿ�������ε����ص�һЩ���Զ����ɶ�Ӧ�Ķ���(**�ɶ�����ɫ�����߼�����ɫ������Ķ���**)���Բ�ֵ������
Ȼ����Щ��ֵ������������Ի���Ϊ������ɫ�����������ݡ�
��������Ĭ������û��ʹ�ü�����ɫ����ͼƬ[6.5](#Image6.5)����������ǵĹ�����

<img src="Images/6.5.png" id = "Image6.5"> </img>

������ɫ�����ƶ�����ɫ������Ҳͬ����һ��������
�������ǻ��ÿ����������һ���������������ص���ɫ��
��������Ҫע���������Ǵ�������ز��������ŵ���̨�����е����أ���Щ���ؿ��ܻᱻ`Clip`�����ü��������߱�����һ�����ֵ��С�����ظ��ǡ�

һЩ�Ѿ�ȷ�������ܳ����ں�̨�����е����ػᱻӲ��ֱ���Ż�������ֱ�Ӳ��������������������ɫ����
����û��ͨ����Ȳ��Ե����ؾ�û�б�Ҫȥ����������ɫ���ˣ���Ϊ�������������ض����ᱻд�뵽��̨������ȥ��

```hlsl
cbuffer PerObject : register(b0)
{
    float4x4 gWorldViewProj;
};

void VsMain(float3 pos : POSITION,
    float4 inputColor : COLOR, 
    out float4 posH : SV_POSITION,
    out float4 outColor : COLOR)
{
    posH = mul(float4(pos, 1.0f), gWorldViewProj);
    
    outColor = inputColor;
}

float4 PsMain(float4 posH : SV_POSITION,
    float4 color : COLOR) : SV_TARGET
{
    return color;

    //lol, ԭ�����ﷵ�ص���pin.Color���Ҽ�ֱ�����ˡ�
}
```

������������У����Ǿ�ֱ���˷��ز�ֵ�����ɫ��
��������Ҫע�����������ɫ�����������Ҫ�Ͷ�����ɫ�����������һ�¡�
ͨ����˵������ɫ���ķ���ֵ��һ��4ά������`SV_TARGET`��־��ζ�����ǵķ���ֵ������Ҫ�ܹ���`Render Target`�ĸ�ʽһ�¡�

����ͬ��Ҳ����ʹ�ýṹ������������Ķ����������ɫ��������������ݡ�

```C++
cbuffer cbPerObject : register(b0)
{
    float4x4 gWorldViewProj;
};

struct VertexIn
{
    float3 Pos : POSITION;
    float4 Color : COLOR;
};

struct VertexOut
{
    float4 PosH : SV_POSITION;
    float4 Color : COLOR;
};

VertexOut Vs(VertexIn inPut)
{
    Vertex outPut;

    outPut.posH = mul(float4(inPut.Pos, 1.0f)), gWorldViewProj);

    outPut.Color = inPut.Color;

    return outPut;
}

float4 Ps(VertexOut inPut) : SV_Target
{
    return inPut.Color;
}
```

## <element id = "6.6"> 6.6 CONSTANT BUFFERS </element>

### <element id = "6.6.1"> 6.6.1 Creating Constant Buffers </element>

���������һ���ܹ�����ɫ���������õ�`GPU`��Դ��
������֮���ѧϰ�У����ǻ�֪�������Լ��������͵Ļ�����Դ���ܹ�����ɫ���������á�

```C++
cbuffer cbPerObject : register(b0)
{
    float4x4 gWorldViewProj;
}
```

�������Ƕ�����һ������`cbPerObject`��`cbuffer`(**Constant Buffer**)��
��������ǵĳ������д洢��һ��4x4�ľ��󣬽�����任�����ӽǾ����Լ�ͶӰ������������ˣ����ڽ�һ�������ģ�Ϳռ���ת������βü��ռ���ȥ��

���񶥵���������壬������ͨ����Ҫ��ÿһ֡��ʱ��ͨ��`CPU`ȥ�������ݡ�
���磬������ǵ��������ÿһ֡��ʱ�����ƶ�����ô���Ǿ���Ҫ��ÿһ֡��ʹ���µ��ӽǾ���������ǵĻ��塣
������Ǵ����������ʱ����Խ����ŵ��ϴ���(**Upload Heap**)������Ĭ�϶��У������Ļ����ǾͿ���ֱ��ʹ��`CPU`ȥ���³������ˡ�

ע����ǣ�������Ŀռ��С������Ӳ���ܹ��������С�Ŀռ��С�ı�������**256bytes**�ı�����

����ͨ��������Ҫʹ�ö��ͬ�����͵Ļ��壬��������֮ǰ��������Ǹ����壬����ÿ����ͬ�����嶼��Ҫʹ�õ���

��`Direct3D 12`�����ǿ���ʹ��`Shader Model 5.1`��������ǿ���ʹ������ķ�������һ�������塣

```hlsl
struct ObjectConstants 
{
    float4x4 gWorldViewProj;
    uint matIndex;
};

ConstantBuffer<ObjectConstants> gObjConstants : register(b0); 
```

### <element id = "6.6.2"> 6.6.2 Updating Constant Buffers </element>

�������ǵĳ������Ǵ������ϴ����еģ�������ǿ���ֱ����`CPU`�и��»��塣
������˵������Ҫ�Ȼ�ȡ��Դ���ݵ�ָ�룬���ǿ���ʹ��`Map`��������ȡ��

```C++
    ComPtr<ID3D12Resource> uploadBuffer;
    BYTE* data = nullptr;
    uploadBuffer.Map(0, nullptr, reinterpret_cast<void**>(&data));
```

`Map`�����ĵ�һ���������Ǳ�ʾ����Ҫ��ȡ������Դ�еڼ�������Դ��ָ�롣
���ڻ�����˵����ֻ��һ������Դ�������Լ���������Ǿ�����Ϊ0��
�ڶ���������������`D3D12_RANGE`����ʾ����Ҫӳ����ڴ淶Χ������Ϊ`null`�Ļ��ʹ�������Ҫӳ�����������Դ��
�����������Ļ����Ƿ�������Ҫ����Դ��ָ�롣

���ǿ���ʹ������ķ��������ݸ��Ƶ���������ȥ:

```C++
    memcpy(data, &SourceData, DataSize);
```

��������ɸ��²���Ҫ�ٸ��µ�ʱ������Ӧ��ʹ��`Unmap`����ȥ�ͷ��ڴ档

```C++
    if (uploadBuffer != nullptr)
        uploadBuffer->Unmap(0, nullptr);

    data = nullptr;
```

��һ������������Ҫ���ĸ�����Դ�����ͷţ����ڻ�����˵����ֻ��Ҫ����Ϊ**0**�ͺ��ˡ�
�ڶ���������������`D3D12_RANGE`��ʾ�������Դ������Ҫ�ͷŵ��ڴ淶Χ������Ϊ`nullptr`��ʾ������Դ��

### <element id = "6.6.3"> 6.6.3 Upload Buffer Helper </element>

Nullptr!!!

### <element id = "6.6.4"> 6.6.4 Constant Buffer Descriptors </element>

�ع�4.1.6��������ͨ������������Դ�󶨵���Ⱦ�ܵ���ȥ�ġ�
������Ϊֹ���ǰ���ȾĿ��(**Render Target**)�����ģ�建��(**Depth/Stencil Buffer**)�Լ��������������(**Vertex/Index Buffer**)����ʹ�õ���������
�������Ҫ�󶨳����嵽�ܵ���ȥ�Ļ�������Ҫ������������
��������������ڵ��������ѵ�������`D3D12_DESCRIPTOR_HEAP_TYPE_CBV_SRV_UAV`��Ҳ����˵������ܹ��洢������(**Constant Buffer**)����ɫ����Դ(**Shader Resource**)��������Դ(**unordered	access**)����������
Ϊ�˴洢��Щ��������������Ҫ����һ�����������͵Ķ����洢��

```C++
    D3D12_DESCRIPTOR_HEAP_DESC	desc;

    desc.NumDescriptors = 1;
    desc.Type = D3D12_DESCRIPTOR_HEAP_TYPE_CBV_SRV_UAV;
    desc.Flags = D3D12_DESCRIPTOR_HEAP_FLAG_SHADER_VISIBLE;
    desc.NodeMask = 0;

    ComPtr<ID3D12DescriptorHeap> cbvHeap;

    device->CreateDescriptorHeap(&desc, IID_PPV_ARGS(&cbvHeap));
```

��Ȼ����������͵ĶѵĴ��������֮ǰ�����������͵ĶѵĴ����ǲ��ģ�����������Ҫע���趨`D3D12_DESCRIPTOR_HEAP_FLAG_SHADER_VISIBLE`�����־(`Flags`)�����������������������ܹ�����ɫ��������ʡ�


������Ҫ���`D3D12_CONSTANT_BUFFER_VIEW_DESC`�ṹ�ſ��Դ�����������������Ȼ��ʹ��`ID3D12Device::CreateConstantBufferView`������������

```C++
    D3D12_CONSTANT_BUFFER_VIEW_DESC desc;

    desc.BufferLocation = bufferVisualAddress;
    desc.SizeInBytes = bufferSize;

    device->CreateConstantBufferView(&desc,
        heap->GetCPUDescriptorHandleForHeapStart());
```

����ͨ��ʹ��`D3D12_CONSTANT_BUFFER_VIEW_DESC`���������е�һ������Դ����һ������󶨵���ɫ���ж�Ӧ�ĳ�����ṹ���С�
֮ǰ����Ҳ�ᵽ��һ������������Դ洢��������������Ϣ������ʹ��ĳһ����Դ��ʱ�����ǿ���ʹ��`BufferLocation`�Լ�`SizeInBytes`��������
��������Ҫע��`D3D12_CONSTANT_BUFFER_VIEW_DESC::SizeInBytes`��`D3D12_CONSTANT_BUFFER_VIEW_DESC::OffsetInBytes`�Ĵ�С������`256bytes`�ı�����

### <element id = "6.6.5">  6.6.5 Root Signature and Descriptor Tables </element>

ͨ����˵����ͬ����ɫ�������ڻ���ָ��ִ��ǰ��Ҫ�󶨵��ܵ�����Դ����������ͬ��
������Դһ����˵���ᱻ�󶨵��ܵ��е�һ������������(**register slots**)���������ǾͿ���ͨ����ɫ�����ʱ��󶨵���Դ�ˡ�

����֮ǰʹ�õ���ɫ����ֻʹ����һ����������ѣ�����֮�����ǻ�ʹ�õ��������Դ�����������������ȣ���Щ������Ҫ�󶨵��ܵ��ϵġ�

```C++
SamplerState gsamPointWrap : register(s0); 
SamplerState gsamPointClamp : register(s1); 
SamplerState gsamLinearWrap	 : register(s2); 
SamplerState gsamLinearClamp : register(s3); 
SamplerState gsamAnisotropicWrap : register(s4);
SamplerState gsamAnisotropicClamp : register(s5);
	
cbuffer PerObject : register(b0)
{
    float4x4 gWorld;
    float4x4 gTexTransform;
};

cbuffer Pass : register(b1)
{
    float4x4 gView;
    float4x4 gProj;

    //Other 
};

cbuffer Material : register(b2)
{
    float4 gDiffuseAlbedo;
    float3 gFresnelR0;
    float gRoughness;
    float4x4 gMatTransform;
}
```

��Դ���(**Root Signature**)�洢�����ڻ���ָ��ִ��ǰҪ�󶨵���Ⱦ�ܵ�����Դ����Щ���Լ���Щ��Դ���ᱻӳ�䵽��ɫ�����ĸ�����Ĵ����С�
��Դ��Ǵ洢�����ݱ��������ʹ�õ���ɫ���������Ǻ�(���ڻ���ָ��ִ��֮ǰ�����Ǳ�������Դ�����������Щ����ɫ�������Լ�ʹ�õ���Դ)��
���ǽ����ڴ�����Ⱦ�ܵ���ʱ����֤���ߵ������Ƿ��Ǻϡ�ע�ⲻͬ�Ļ���ָ�������Ҫ��ͬ����ɫ�������Լ���ͬ����Դ��ǡ�

���ǿ��Լ�����ɫ��������һ������������ɫ����ʹ�õ���Դ�ǲ�������ô���ǿ�����Ϊ��Դ��Ǿ����������������ġ�

��`Direct3D`������ʹ��`ID3D12RootSignature`����ʾһ����Դ��ǡ�
�����洢һ����Դ��������������Ҫ����ɫ����ʹ����Щ��Դ��
һ����Դ���������ǳ�����������������������
������֮��ὲ���������������������ڱ��������ǻ�ʹ�õ���������
�������������������ѵĻ����ϣ�ָ���ѵ�һ�η�Χ��Ϊ����Ҫʹ�õ�һ����������

```C++
//����������´���

    D3D12_DESCRIPTOR_RANGE cbvRange;
    D3D12_ROOT_PARAMETER rootParameter[1];

    cbvRange.RangeType = 
    cbvRange.NumDescriptors = 1;
    cbvRange.BaseShaderRegister = 0;

    rootParameter.ParameterType = D3D12_ROOT_PARAMETER_TYPE_DESCRIPTOR_TABLE;
    rootParameter.ShaderVisibility = D3D12_SHADER_VISIBILITY_ALL;
    rootParameter.DescriptorTable.NumDescriptorRanges = 1;
    rootParameter.DescriptorTable.pDescriptorRanges = &cbvRange;

//�����ȿ��ţ����ٲ���û����MSDN��������
```


֮ǰ˵����Դ���ֻ�Ƕ���������Ҫ�󶨵���Ⱦ�ܵ�����Դ��
������ζ������Ҫ�ڴ�������ʱ��ͽ���Դ�󶨵���Ⱦ�ܵ��ϡ�
���ǻ�ʹ��ָ���б�����������Ҫʹ�õ���Դ��׼ȷ��˵�Ǻ���`ID3D12GraphicsCommandList::SetGraphicsRootDescriptorTable`��

```C++
    void ID3D12GraphicsCommandList::SetGraphicsRootDescriptorTable(
        UINT RootParameterIndex,
        D3D12_GPU_DESCRIPTOR_HANDLE BaseDescriptor);
```

- `RootParameterIndex`: ����Ҫ���õ��ĸ���Դ������ȥ��
- `BaseDescriptor`:  