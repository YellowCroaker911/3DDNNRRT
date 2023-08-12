# 1 ����
����Ŀּ�ڸ�������[Deep Neural Network Enhanced Sampling-Based Path Planning in 3D Space](https://ieeexplore.ieee.org/abstract/document/9598184)�ĺ����㷨������ģ�Ͳ������е�3D DNN Models����Ŀǰ����ģ�Ͳ���δ�ܸ��֣��������⽫�����Ĳ�����
# 2 ��������
## 2.1 ��ѻ������ã�����ʿ���ǰ���ݣ�
- MATLAB�汾2023a
- python�汾3.8��torch�汾1.13.1+cu116��torchvision�汾0.14.1+cu116��d2l�汾0.17.6
## 2.2 �������ù���
### 2.2.1 python��������
#### 1 ���塶����ѧ���ѧϰ����Դ
- [�ٷ���վ](https://d2l.ai)
- [�ٷ���Ƶ](https://www.bilibili.com/video/BV1if4y147hS)
#### 2 Anaconda��������
- [����Anaconda](https://www.anaconda.com)����װʱ��ѡ����ӵ�����������
- ��Anaconda Prompt
- �������⻷����`conda create -n d2l python=3.8`������python3.8�汾Ϊ����
- �������⻷����`conda activate d2l`
- ��װd2l�⣺`pip install jupyter d2l -i https://pypi.tuna.tsinghua.edu.cn/simple`
#### 3 CUDA��������
- ��NVIDIA������壬���������������ϵͳ��Ϣ�������������3D���õ�3��NVCUDA64.DLL��鿴���֧��CUDA�汾������CUDA�汾11.6Ϊ����
- [����CUDA](https://developer.nvidia.com/cuda-11-6-0-download-archive)����װʱ��ο�[��Ƶ����](https://www.bilibili.com/video/BV1nL4y1b7oT/)��ע����ڸ߰汾���µ��Ͱ汾��ѡ�Ҫ��ѡ����װʧ��ʱ��ο�[��������](https://blog.csdn.net/Redamancy06/article/details/125760678)��������Ҫȡ����ѡHsight VSE��
- [����cuDNN](https://developer.nvidia.cn/rdp/cudnn-download)��ע������ǰ��Ҫע��NVIDIA�������˻���
- [����pytorch��torchvision](https://download.pytorch.org/whl/torch_stable.html)����װ���汾��cu116/torch-1.13.1%2Bcu116-cp38-cp38-win_amd64.whl��cu116/torchvision-0.14.1%2Bcu116-cp38-cp38-win_amd64.whl����װ��
```
conda activate d2l
pip install ��·����/torch-1.13.1%2Bcu116-cp38-cp38-win_amd64.whl��
pip install ��·����/torchvision-0.14.1%2Bcu116-cp38-cp38-win_amd64.whl��
```
#### 4 pycharm��������
- [����pycharm <u>Community</u>�汾](https://www.jetbrains.com/pycharm/download/?section=windows)����װ��
- ��pycharm�д�������Ŀ��������Ŀ��python������Ϊconda���⻷��d2l���ο�[��������](https://blog.csdn.net/weixin_42009054/article/details/100144417)��
- ����pycharm���ն�ΪAnaconda Prompt,�ο�[��������](https://blog.csdn.net/jialong_chen/article/details/122092510)��
#### 5 ��֤����
- ���룺
```
import torch 
import torchvision 
print(torch.__version__) 
print(torchvision.__version__) 
print(torch.cuda.is_available())
```
- �����
```
1.13.1+cu116
0.14.1+cu116
True
```
6 ʹ��Jupyter�Ķ�����
��[�ٷ���վ](https://d2l.ai)�����Jupyter���±���������Դ������ԴĿ¼��ִ�У�
```
conda activate d2l
Jupyter Notebook
```
### 2.2.2 MATLAB��pytorch����
- [�ٷ���Ƶ](https://www.bilibili.com/video/BV1WY4y17728)������3��Matlab��TesnorFlow/Pytorch��ܽ����ķ���
- ����Ŀ�ο�[֪������](https://zhuanlan.zhihu.com/p/536858806)ʵ���˴�MATLAB������Pytorch��Ԥѵ���õ�����ģ�ͣ�����[�ٷ���Ƶ](https://www.bilibili.com/video/BV1WY4y17728)���ᵽ�ĵ�һ�ַ�����Co-execution between MATLAB and TensorFlow/Pytorch����
# 3 �㷨����ժҪ
�㷨����������˵���Ի���Ϊ3���֣�
- ���ݼ�����
- ����ģ�͹���
- 3D Neural RRT*�㷨·���滮
## 3.1 ���ݼ�����
����ԭ���ģ������2�������1�������Ϊ1ͨ��32\*32\*32���ݡ����У�Ԫ�ص�ֵ�����ò������²���[0,1]���䣬���ڴˣ��裺

��1������**X1**Ϊ�ϰ���ͼ����(0�������ϰ���1�����ϰ�����<br>
��2������**X2**Ϊ������ʼ�����յ�λ�õľ���(0������������1�����������յ�Ϊ����3\*3\*3������)��<br>
���**Y**Ϊpromising region(��ǰ��������)��ԭʼ���󣬾���������һ��ֵ��Ԫ�ش���promising region��<br>
�����ѵ��Ŀ��**T**����A*�㷨���ɵ��ؿ�·����Ϊpromising region��0������������1�����ؿ�·���ϵ����򣩡�

��ˣ����������������Ա�ʾΪ[**X1**,**X2**,**T**]
### 3.1.1 ���ɵ�һ������**X1**
��ʼ��32\*32\*32ȫ0��ͼ��������������ɸ��������ϰ����������ڶ�Ӧλ�õ�Ԫ��ֵ��Ϊ1���ɵõ�**X1**��
### 3.1.2 ���ɵڶ�������**X2**
��ʼ��32\*32\*32ȫ0��ͼ���󣬸���**X1**�����ϰ�������������յ㣬�ٽ��������յ�Ϊ����3\*3\*3�����ӦԪ�ص�ֵ��Ϊ1������ʱ��Ҫ��֤�����յ���Χ3\*3\*3����Ҳ�������ϰ������õ�**X2**��
### 3.1.3 ����Ŀ��**T**
��ʼ��32\*32\*32ȫ0��ͼ���󣬸���**X1**�͹���**X2**ʱ�õ��������յ㣬ִ��A*�㷨����·���滮�����ؿ�����·�������ؿ�·�������Ӧ��Ԫ��ֵ��Ϊ1���õ�**T**��

��Ҫע����ǣ�A*�㷨�������ͼ����**X1**ת���õ���դ���ͼ��MATLAB�е�ocuupancyMap3D����������һ���̶ȵ����ͣ��������ͳ̶�ȡ�������˻��İ뾶����

### 3.1.4 ���ݵ���֯

�����ɵ�������������Ϊn����������Ҫ��n��������������[**X1**,**X2**,**T**]��֯Ϊn��3�е�Cell�ṹ������Ϊ.mat�ļ������治�ٽ�[**X1**,**X2**,**T**]������������������������Ϊһ�����������ݡ�
#### 1 MATLAB�е���֯
ͨ�����д��뽫����ת��ΪMATLAB�����繤�����ж������������������֯��ʽ��
```
function ds = loadData(name)
% ��·��name�е�.mat�ļ�תΪTransformDataStore���ͣ��������ܴ���MATLAB�������磩
    fds = fileDatastore(name,ReadFcn=@load);
    ds = transform(fds,@transformFcn);
end

function dsNew = transformFcn(ds1)
    dsNew = ds1.dataCell;
end
```

�������������ݵ�һ�ֶ��巽ʽ��
```
mbq = minibatchqueue(ds,...
MiniBatchSize=miniBatchSize, ...
MiniBatchFcn=@preprocessMiniBatch, ...
MiniBatchFormat="SSSCB");
```

ͨ��```[X1,X2,T] = next(mbq);```���Խ�һ������������ȡ���������뵽����ģ�ͽ���ѵ����Ԥ�⡣

#### 2 pytorch�е���֯
�����Զ���һ��2����1�����dataloader����:
```
class MyDataset(Dataset):
    """
    �Զ���2����1�����dataloader����
    """
    def __init__(self, data):
        self.data = data

    def __len__(self):
        return len(self.data)

    def __getitem__(self, index):
        input1, input2, output = self.data[index]
        return torch.Tensor(input1), torch.Tensor(input2), torch.Tensor(output)
```

�ٶ�ȡ����õ����ݼ�.mat�ļ���Ϊ�����Դ���dataloader��pytorch���������ѵ��:

```
def load_data(batch_size,dataloader_workers=0):
    """
    ��.mat�ļ��м������ݣ�תΪdataloader����
    """
    mat_train = scipy.io.loadmat('../trainData.mat')
    mat_test = scipy.io.loadmat('../testData.mat')
    mat_train = mat_train['dataCell']
    mat_test = mat_test['dataCell']
    data_train = MyDataset(mat_train)
    data_test = MyDataset(mat_test)
    return (DataLoader(data_train,batch_size,shuffle=True,num_workers=dataloader_workers),
            DataLoader(data_test,batch_size,shuffle=True,num_workers=dataloader_workers))
```

����Ԥ�⣬��Ҫע������ڽ�����������ת������Ҫת������������ά�ȵ����з�ʽ,����SSSCB������ά������ά������ά��ͨ��ά������ά��תΪBCSSS������Ԥ�����Ҳͬ���˲�������python����ɣ�
```
X1 = np.asarray(X1)
X1 = torch.from_numpy(X1)
X1 = X1.type(torch.FloatTensor)
X1 = torch.Tensor(X1).permute(4, 3, 0, 1, 2)
# ...
```
```
with torch.no_grad():
    pred = model(X1,X2)
pred = torch.Tensor(pred).permute(2, 3, 4, 1, 0)
pred = pred.detach().numpy()
pred = np.ascontiguousarray(pred)
```

## 3.2 ����ģ�͹���
�⸴��ԭ�������ᵽ��3D CNN Model:
<img src="picture/3D CNN model.jpg" alt="3D CNN Model">
�乹��Դ��[U-net: Convolutional networks for biomedical image segmentation](https://link.springer.com/chapter/10.1007/978-3-319-24574-4_28):
<img src="picture/U-net.jpg" alt="U-net">
����ԭ�����е�����������U-net��decoder��encoder���ֶ�Ӧ����4��3\*3\*3�ľ���㣬Ȼ����Щ��Ϣ�������Թ���һ�����������ܹ������ң�����Ŀ�ڸ���U-net���Թ�����������ܹ�ʱ�����ֲ�����ͬʱ����ԭ���ĵ��е�����������

### 3.2.1 ����ģ�͵����ɲ���
#### 1 ��MATLAB����������ģ��
��MATLAB�У���������ģ�ͽ�Ϊ����ķ��������������������н����еĲ���в������ò����Ӻú�ֱ�ӵ�������ģ�ͣ�����ģ����.net�ļ�����ʽ���档���ţ�������ģ�ͺ����ݼ����뵽ѵ��ѭ���У�ͨ��ѵ���Ը�������ģ���еĲ������õ�ѵ���õ����硣�Զ���ѵ��ѭ����ο�[Train Network Using Custom Training Loop](https://ww2.mathworks.cn/help/deeplearning/ug/train-network-using-custom-training-loop.html)��
#### 2 ��pytorch����������ģ��
��pytorch�У����Ը��ݴ���������ģ�͵��ص������зֿ飬������ɲ�ͬ���࣬������ܵ�����ṹ�а�����Щ���ʵ����
```
class MyConv3dBlock(nn.Module):
    """
    encoder·���ľ����
    """
    def __init__(self, input_channels, num_channels):
        super().__init__()
        self.conv = nn.Conv3d(in_channels=input_channels,
                              out_channels=num_channels,
                              kernel_size=(3, 3, 3),
                              stride=(1, 1, 1),
                              padding =(1, 1, 1))
        self.mp = nn.MaxPool3d(kernel_size=(2, 2, 2),
                               stride=(2, 2, 2))
        self.bn = nn.BatchNorm3d(num_channels)
        self.rl = nn.ReLU()

    def forward(self, X):
        Y = self.rl(self.bn(self.mp(self.conv(X))))
        return Y


class MyConvTransPose3dBlock(nn.Module):
    """
    decoder·����ת�þ����
    """
    def __init__(self, input_channels, num_channels):
        super().__init__()
        self.tpconv = nn.ConvTranspose3d(in_channels=input_channels,
                                         out_channels=num_channels,
                                         kernel_size=(2, 2, 2), # origin paper������3*3*3��������޷����������Сǡ�÷���
                                         stride=(2, 2, 2),
                                         padding=(0, 0, 0))
        self.bn = nn.BatchNorm3d(num_channels)
        self.rl = nn.ReLU()

    def forward(self, X):
        Y = self.rl(self.bn(self.tpconv(X)))
        return Y


class MyUnet(nn.Module):
    """
    �鿴����ܹ���
    net = MyUnet()
    net = net.to("cuda")
    summary(net,[(1,32,32,32),(1,32,32,32)])
    """
    def __init__(self):

        # encoder
        super().__init__()
        self.my_conv3d1a = MyConv3dBlock(1,16)
        self.my_conv3d1b = MyConv3dBlock(1,16)
        self.my_conv3d2 = MyConv3dBlock(32,64)
        self.my_conv3d3 = MyConv3dBlock(64,128)
        self.my_conv3d4 = MyConv3dBlock(128,256)

        # decoder
        self.my_tpconv3d1 = MyConvTransPose3dBlock(256,128)
        self.my_tpconv3d2 = MyConvTransPose3dBlock(128,64)
        self.my_tpconv3d3 = MyConvTransPose3dBlock(64, 32)
        self.my_tpconv3d4 = MyConvTransPose3dBlock(32, 16)
        self.conv1x1 = nn.Conv3d(16, 1, kernel_size=(1,1,1), stride=(1,1,1), padding=(0,0,0))
        self.sig = nn.Sigmoid()


    def forward(self, x1, x2):
        # encoder
        out1a = self.my_conv3d1a(x1)
        out1b = self.my_conv3d1b(x2)
        out1ab = torch.cat([out1a, out1b], dim=1)
        out2 = self.my_conv3d2(out1ab)
        out3 = self.my_conv3d3(out2)
        out4 = self.my_conv3d4(out3)

        # decoder
        out5 = self.my_tpconv3d1(out4)
        out6 = self.my_tpconv3d2(out5 + out3)
        out7 = self.my_tpconv3d3(out6 + out2)
        out8 = self.my_tpconv3d4(out7 + out1ab)
        out9 = self.conv1x1(out8)
        out10 = self.sig(out9)

        return out10

```
���Ƶأ�������ģ�ͺ����ݼ����뵽ѵ��ѭ���У��õ�ѵ���õ����硣pytorch�е��Զ���ѵ��ѭ���ɲο�d2l���```d2l.train_ch6()```������

### 3.2.2 ����ṹ�鿴
#### 1 ��MATLAB�в鿴����ṹ
��MATLAB�У���ͨ��```analyzeNetwork()```���������dlnetwork���Ͳ鿴����ṹ��
<img src="picture/analyzeNetwork.jpg" alt="analyzeNetwork">
Ҳ����ͨ�����������������е��������lgraph���Ͳ鿴����ṹ��
<img src="picture/dlnetworkDesigner.jpg" alt="dlnetworkDesigner">

#### 2 ��pytorch�в鿴����ṹ
��pytorch�У���ͨ��```torchsummary.summary()``����������ݴ�С���鿴����Ľṹ:
```
net = MyUnet()
net = net.to("cuda")
summary(net,[(1,32,32,32),(1,32,32,32)])
```
������£�
```
----------------------------------------------------------------
        Layer (type)               Output Shape         Param #
================================================================
            Conv3d-1       [-1, 16, 32, 32, 32]             448
         MaxPool3d-2       [-1, 16, 16, 16, 16]               0
       BatchNorm3d-3       [-1, 16, 16, 16, 16]              32
              ReLU-4       [-1, 16, 16, 16, 16]               0
     MyConv3dBlock-5       [-1, 16, 16, 16, 16]               0
            Conv3d-6       [-1, 16, 32, 32, 32]             448
         MaxPool3d-7       [-1, 16, 16, 16, 16]               0
       BatchNorm3d-8       [-1, 16, 16, 16, 16]              32
              ReLU-9       [-1, 16, 16, 16, 16]               0
    MyConv3dBlock-10       [-1, 16, 16, 16, 16]               0
           Conv3d-11       [-1, 64, 16, 16, 16]          55,360
        MaxPool3d-12          [-1, 64, 8, 8, 8]               0
      BatchNorm3d-13          [-1, 64, 8, 8, 8]             128
             ReLU-14          [-1, 64, 8, 8, 8]               0
    MyConv3dBlock-15          [-1, 64, 8, 8, 8]               0
           Conv3d-16         [-1, 128, 8, 8, 8]         221,312
        MaxPool3d-17         [-1, 128, 4, 4, 4]               0
      BatchNorm3d-18         [-1, 128, 4, 4, 4]             256
             ReLU-19         [-1, 128, 4, 4, 4]               0
    MyConv3dBlock-20         [-1, 128, 4, 4, 4]               0
           Conv3d-21         [-1, 256, 4, 4, 4]         884,992
        MaxPool3d-22         [-1, 256, 2, 2, 2]               0
      BatchNorm3d-23         [-1, 256, 2, 2, 2]             512
             ReLU-24         [-1, 256, 2, 2, 2]               0
    MyConv3dBlock-25         [-1, 256, 2, 2, 2]               0
  ConvTranspose3d-26         [-1, 128, 4, 4, 4]         262,272
      BatchNorm3d-27         [-1, 128, 4, 4, 4]             256
             ReLU-28         [-1, 128, 4, 4, 4]               0
MyConvTransPose3dBlock-29         [-1, 128, 4, 4, 4]               0
  ConvTranspose3d-30          [-1, 64, 8, 8, 8]          65,600
      BatchNorm3d-31          [-1, 64, 8, 8, 8]             128
             ReLU-32          [-1, 64, 8, 8, 8]               0
MyConvTransPose3dBlock-33          [-1, 64, 8, 8, 8]               0
  ConvTranspose3d-34       [-1, 32, 16, 16, 16]          16,416
      BatchNorm3d-35       [-1, 32, 16, 16, 16]              64
             ReLU-36       [-1, 32, 16, 16, 16]               0
MyConvTransPose3dBlock-37       [-1, 32, 16, 16, 16]               0
  ConvTranspose3d-38       [-1, 16, 32, 32, 32]           4,112
      BatchNorm3d-39       [-1, 16, 32, 32, 32]              32
             ReLU-40       [-1, 16, 32, 32, 32]               0
MyConvTransPose3dBlock-41       [-1, 16, 32, 32, 32]               0
           Conv3d-42        [-1, 1, 32, 32, 32]              17
          Sigmoid-43        [-1, 1, 32, 32, 32]               0
================================================================
Total params: 1,512,417
Trainable params: 1,512,417
Non-trainable params: 0
----------------------------------------------------------------
Input size (MB): 4096.00
Forward/backward pass size (MB): 37.69
Params size (MB): 5.77
Estimated Total Size (MB): 4139.46
----------------------------------------------------------------
```

### 3.2.3 3D CNN model�Ŀ�
����Ŀ�������3D CNN model��������ܹ���Ҫ������ԭ������δ�ἰ��3D CNN model�Ŀ��ϸ�ڣ����ಿ����ԭ���Ļ���һ�¡�
#### 1 decoder��
����Ŀ������ľ�������ܹ���decoder���ֵ�һ�������3\*3\*3����㡢���ػ��㡢������һ���㡢Relu�㣺
```
tempLayers = [
    convolution3dLayer([3 3 3],64,"Name","conv3d_3","Padding",[1 1 1;1 1 1],"WeightsInitializer","he")
    maxPooling3dLayer([2 2 2],"Name","maxpool3d_3","Padding","same","Stride",[2 2 2])
    batchNormalizationLayer("Name","batchnorm_3")
    reluLayer("Name","relu_3")];
```
```
class MyConv3dBlock(nn.Module):
    """
    encoder·���ľ����
    """
    def __init__(self, input_channels, num_channels):
        super().__init__()
        self.conv = nn.Conv3d(in_channels=input_channels,
                              out_channels=num_channels,
                              kernel_size=(3, 3, 3),
                              stride=(1, 1, 1),
                              padding =(1, 1, 1))
        self.mp = nn.MaxPool3d(kernel_size=(2, 2, 2),
                               stride=(2, 2, 2))
        self.bn = nn.BatchNorm3d(num_channels)
        self.rl = nn.ReLU()

    def forward(self, X):
        Y = self.rl(self.bn(self.mp(self.conv(X))))
        return Y
```
#### 2 encoder��
encoder���ֵ�һ�������ת�þ���㡢������һ���㡢Relu�㣺
```
tempLayers = [
    additionLayer(2,"Name","addition_1")
    transposedConv3dLayer([3 3 3],64,"Name","transposed-conv3d_1","Cropping","same","Stride",[2 2 2])
    batchNormalizationLayer("Name","batchnorm_7")
    reluLayer("Name","relu_7")];
lgraph = addLayers(lgraph,tempLayers);
```
```
class MyConvTransPose3dBlock(nn.Module):
    """
    decoder·����ת�þ����
    """
    def __init__(self, input_channels, num_channels):
        super().__init__()
        # ԭ���ĵ�kernel_size��3*3*3��������޷����������Сǡ�÷���
        self.tpconv = nn.ConvTranspose3d(in_channels=input_channels,
                                         out_channels=num_channels,
                                         kernel_size=(2, 2, 2), 
                                         stride=(2, 2, 2),
                                         padding=(0, 0, 0))
        self.bn = nn.BatchNorm3d(num_channels)
        self.rl = nn.ReLU()

    def forward(self, X):
        Y = self.rl(self.bn(self.tpconv(X)))
        return Y
```

Ҳ��������MATLAB��pytorch��API���죬MATLAB�е�ת�þ��������Ϊ3\*3\*3��С����pytorch�е�ת�þ��ֻ������Ϊ2\*2\*2��С�����������ݴ�С�����뵽����ı仯��pytorch��3Dת�þ����```nn.ConvTranspose3d()```�Ĳ���```kernel_size```��```stride```��```padding```�������С��Ӱ�����£�
<img src="picture/ConvTransPose.jpg" alt="ConvTransPose">

### 3.2.4 ��ʧ����
ԭ�����е���ʧ����ʹ���˶�Ԫ�����صľ�ֵ������Ŀ��������Ӧλ�õ�����ֵ��(value-pair)ȡ��Ԫ�����غ���ȡ��ֵ��Ȼ��MATLAB���޴���ʧ�������ʼ��ý����غ���```crossentropy();```��Ϊ��������ú��������ݵ�����һά��Ϊ��ǩ����Ҫ������ͼ�����Ԥ�⣬���������ǵ�Ҫ��pytorch������ڴ���ʧ����```nn.BCELoss()```��

## 3.3 3D Neural RRT*�㷨·���滮
ԭ�����е�3D Neural RRT\*��ԭʼRRT\*�㷨����������ڲ������衣ԭʼRRT\*�㷨�ڲ���ʱ��0.5�ĸ������������Ϳռ��н��о��Ȳ���������0.5�ĸ�����Ŀ�����Ϊ�����㣻3D Neural RRT\*����Ŀ�����Ϊ�������滻Ϊ��promising�ķǾ��Ȳ����������ҵ�·��ǰ�����ò�ͬ����ֵ����Ӧ������������������
```
function samplePoint = sample_point(mapSize,goalNode,promisingRegion,hadfoundFeisiblePath)
    mu1 = 0.9; mu2 = 0.5;
    mu = mu1;
    if hadfoundFeisiblePath
        mu = mu2;
    end
    if rand() < mu                              % �Ǿ��Ȳ���
        idx = randi([1,promisingRegion.Count]);
        samplePoint = promisingRegion.Location(idx,:);
    else                                        % ���Ȳ���
        samplePoint = rand(1,3).*mapSize;
        % % % ��׼RRT/RRT*�㷨���з���һ������ȡĿ�����Ϊ������Ļ��ƣ�����
        % % if rand() < 0.5
        % %     samplePoint = rand(1,3).*mapSize;
        % % else
        % %     samplePoint = goalNode.pos;
        % % end
    end
end
```
���ԭʼRRT\*�㷨��3D Neural RRT\*������Ҳֻ�Ƕ��promising region��һ���룺
```
function [path,nodeList] = RRTstar(map,mapSize,startPos,goalPos,iterMax,step,promisingRegion)
% ���룺mapΪռ��դ���ͼ��mapSizeΪ��ͼ�ߴ��С��startPosΪ��㣬goalPosΪĿ���
%      iterMaxΪ������������stepΪ����������promisingRegionΪ��������
    str = initParam("RRTstar");
    visualization = str.visualization;

    startNode = RRTnode(startPos,nan,1); 
    goalNode = RRTnode(goalPos,nan,nan);
    nodeList = startNode;
    hadfoundFeisiblePath = false;
    path = [];

    pathLineHandles = [];
    treeLineHandles = [];

    for i=1:iterMax
        disp("Current Iterations:"+i);
        samplePoint = sample_point(mapSize,goalNode,promisingRegion,hadfoundFeisiblePath);
        nearestNode = find_nearest_node(nodeList,samplePoint);
        newNode = generate_new_node(step,samplePoint,nearestNode);
        nearNodes = find_near_nodes(step*2,nodeList,newNode);
        sortedNearNodes = sort_near_nodes_bycost(nodeList,newNode,nearNodes);
        fatherNode = pick_father_node(map,newNode,sortedNearNodes);
        if isempty(fatherNode) == false
            [nodeList,newNode] = add_new_node(nodeList,newNode,fatherNode);
            nodeList = rewrite(map,nodeList,newNode,sortedNearNodes);
            if hadfoundFeisiblePath == false
                isfoundFeisiblePath = foundedpath_judge(map,goalNode,step,newNode);
            end
            if isfoundFeisiblePath
                if hadfoundFeisiblePath == false
                    [nodeList,goalNode] = add_new_node(nodeList,goalNode,newNode);
                    hadfoundFeisiblePath = true;
                else
                    path = get_path(nodeList,goalNode);
                    pathLineHandles = show_path(path,pathLineHandles,visualization);
                end
            end
        end
        treeLineHandles = show_nodeList(nodeList,treeLineHandles,visualization);
    end
end
```
# 4 ʵ����������
ʵ�ֹ��̵�����������������Ҫ������ģ�͵����������Ԥ�ڣ���������趨��ֵ������promising region��������ԭ������������һ�����������Ŀ���֮��ķ�����򡣵��趨��ֵΪ0.2�������Y�д���0.2��ֵ����Ӧλ�õļ�������promising region�����Է�������promising region��Ҫ�����������յ㣬���ҷǳ���ɢ��
<img src="picture/generated promising region.jpg" alt="U-net">
�ɴ��϶�����ģ�Ͳ�û�еõ�����ѵ����

������ĳ��ֿ������������ص��£�
#### 1 ���ݵ�ֵ�����ò�ǡ��
ԭ�����в�δ�ἰ���������д���ͬ״̬��Ԫ�ص�ֵ������ã���˱���Ŀ��[3.1](#31-���ݼ�����)����������ֵ��
#### 2 ����ṹ���������������������
ԭ�����ж�����ṹ�������ǳ�����ȷ������������������Ψһȷ��һ�����������ܹ�����˱���Ŀ��[3.2](#32-����ģ�͹���)����������������ܹ������п�����ԭ������˼��ͬ�ĵط����������ƺ��ϲ�����ת�þ��������ơ�
#### 3 ��ʧ��������ȷ
ԭ���������õ���ʧ����ӦΪ��Ԫ�����أ���[3.2.4](#324-��ʧ����)���������ڿ��Կ϶�MATLAB�е���ʧ����������ԭ�����е���ʧ��������pytorch�е���ʧ����Ӧ������ȷ�ġ�
#### 4 ѵ���������ò���
����Ŀ�Ѿ�����U-net��ѵ��������ѵ���������г�ʼ������Ծ�����Ȩ��ִ�кο�����ʼ����ʹ��SGDM�����Ż��ȡ�

������ģ�͵����������Ԥ���⣬����Ŀ��û��ʵ�ֽ����ɵ�ѵ������ӳ����32\*32\*32�������Ŀ�꣬��ԭ���������ἰ�Ķ����������ϴ󣩳ߴ�������ͼ������ѹ����32\*32\*32�����뵽����ģ���н���ѵ����

���⣬��Ϊ·���滮�㷨���㷨�ĸ�Ч����Ҫ���Ա�֤��Ȼ������Ŀ���ǽ��г������㷨���֣������е����ط���û�н����Ż���������A\*�㷨��3D Nerual RRT\*�㷨�ڲ���������ظ����ࡣ