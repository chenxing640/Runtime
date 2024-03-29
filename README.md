如果此项目能帮助到你，就请你给[一颗星](https://github.com/chenxing640/Runtime)。谢谢！(If this project can help you, please give it [a star](https://github.com/chenxing640/Runtime). Thanks!)


[![License MIT](https://img.shields.io/badge/license-MIT-green.svg?style=flat)](LICENSE)&nbsp;


## Notification

**本项目中关于Runtime包装类及内部代码已被废弃，请使用以下 [Swift] 和 [Objective-C] 最新版本：**

- [DYFSwiftRuntimeProvider](https://github.com/chenxing640/DYFSwiftRuntimeProvider) - [Swift] Runtime包装，可快速使用字典转模型、归档解档、添加一个方法、交换两个方法、获取所有属性名和方法名。

- [DYFRuntimeProvider](https://github.com/chenxing640/DYFRuntimeProvider) - [Objective-C] Runtime包装，可快速使用字典转模型、归档解档、添加一个方法、交换两个方法、获取所有属性名和方法名。

**注意：示例和API调用仍然可以作为参考。**


## Runtime

Runtime包装和运用示例，可快速使用字典转模型、归档解档、添加一个方法、交换两个方法、获取某类所有属性名和方法名。另外，在示例中也介绍了添加[分类属性](https://github.com/chenxing640/Runtime/blob/master/RuntimeUsage/RuntimeUsage/Model/YFModel%2BAddingAttr.m)。


## Group (ID:614799921)

<div align=left>
&emsp; <img src="https://github.com/chenxing640/Runtime/raw/master/images/g614799921.jpg" width="30%" />
</div>


## Preview

<div align=left>
&emsp; <img src="https://github.com/chenxing640/Runtime/raw/master/images/runtime-usage.gif" width="30%" />
</div>


## Usage

### 一、导入头文件 

```
#import "DYFRuntimeWrapper.h"
```

### 二、Runtime 运用介绍

- 获取所有方法名，例如获取UITableView的方法名

```
NSArray *list = [DYFRuntimeWrapper yf_getAllMethodsWithClass:[UITableView class]];

for (NSString *name in list) {
    NSLog(@">>>> %@", name);
}
```

- 获取所有属性名，例如获取UILabel的属性变量

```
NSArray *list = [DYFRuntimeWrapper yf_getAllIvarsWithClass:[UILabel class]];

for (NSString *name in list) {
    NSLog(@">>>> %@", name);
}
```

- 添加一个方法

```
+ (void)load {
    [DYFRuntimeWrapper yf_addMethodWithClass:[self class] methodName:@"hello" impClass:[self class] impName:@"sayHello"];
}

- (void)viewDidLoad {
    [super viewDidLoad];

#pragma clang diagnostic push
#pragma clang diagnostic ignored "-Warc-performSelector-leaks"
    [self performSelector:NSSelectorFromString(@"hello")];
#pragma clang diagnostic pop
}

- (void)sayHello {
    NSLog(@">>>> %s", __FUNCTION__);
}
```

- 交换两个方法

```
- (IBAction)exchangeMethod:(id)sender {
    [DYFRuntimeWrapper yf_exchangeMethodWithSourceClass:[self class] sourceSel:@selector(preload) targetClass:[self class] targetSel:@selector(refreshUI)];
}

- (void)preload {
    NSString *str = [NSString stringWithFormat:@"%s\n", __FUNCTION__];
    NSLog(@">>>> str1: %@", str);
    self.displayView.text = [self.displayView.text stringByAppendingString:str];
    NSLog(@">>>> self.displayView.text: %@", self.displayView.text);
}

- (void)refreshUI {
    NSString *str = [NSString stringWithFormat:@"%s-%@\n", __FUNCTION__, self.randomString];
    NSLog(@">>>> str2: %@", str);
    self.displayView.text = [self.displayView.text stringByAppendingString:str];
    NSLog(@">>>> self.displayView.text: %@", self.displayView.text);
}

- (NSString *)randomString {
    uint32_t a = arc4random_uniform(2);
    unsigned int len = (a == 1) ? 40 : 32;
    
    char data[len];
    
    for (int x = 0; x < len; x++) {
        uint32_t ar = arc4random_uniform(2);
        char ch = (char)(((ar == 1) ? 'A' : 'a') + (arc4random_uniform(26)));
        data[x] = ch;
    }
    
    return [[NSString alloc] initWithBytes:data length:len encoding:NSUTF8StringEncoding];
}
```

- 替换某个方法

```
- (IBAction)replaceMethod:(id)sender {
    [DYFRuntimeWrapper yf_replaceMethodWithSourceClass:[self class] sourceSel:@selector(preload) targetClass:[self class] targetSel:@selector(refreshUI)];
}
// 同上
```

- 字典转模型

```
- (IBAction)dictToModel:(id)sender {
    NSDictionary *dict = @{@"name": self.nameTF.text,
                           @"gender": self.genderTF.text,
                           @"age": self.ageTF.text};
    YFModel *model = [DYFRuntimeWrapper yf_modelWithDict:dict modelClass:[YFModel class]];
    self.displayView.text = [self.displayView.text stringByAppendingString:[NSString stringWithFormat:@"\nname: %@\ngender: %@\nage: %@", model.name, model.gender, model.age]];
}
```

- 归档解档

```
- (void)viewDidLoad {
    [super viewDidLoad];
    // 创建路径
    NSString *documentPath      = [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory,NSUserDomainMask, YES) lastObject];
    NSString *filePath          = [documentPath stringByAppendingPathComponent:@"YFModel.plist"];
    self.filePath               = filePath;
}

- (IBAction)archive:(id)sender {
    YFModel *model = [YFModel new];
    model.name     = self.nameTF.text;
    model.gender   = self.genderTF.text;
    model.age      = self.ageTF.text;

    BOOL ret = [DYFRuntimeWrapper yf_archive:[model class] model:model filePath:self.filePath];
    if (ret) {
        NSLog(@">>>> 归档成功：%@", self.filePath);
    } else {
        NSLog(@">>>> 归档失败！！！");
    }
}

- (IBAction)unarchive:(id)sender {
    self.displayView.text = @"解档后的数据：";

    YFModel *m = [DYFRuntimeWrapper yf_unarchive:[YFModel class] filePath:self.filePath];

    self.displayView.text = [self.displayView.text stringByAppendingString:[NSString stringWithFormat:@"\nname: %@\ngender: %@\nage: %@", m.name, m.gender, m.age]];
}
```

- 添加分类属性

1. 导入头文件`#import <objc/message.h>`

2. 声明属性

```
/** 居住地址 */
@property (nonatomic, copy) NSString *address;
```

3. 申明一个key值

```
static NSString *kHomeAddress = @"kHomeAddress";
```

4. 重写setter、getter方法

```
// get方法
- (NSString *)address {
    return objc_getAssociatedObject(self, &kHomeAddress);
}

// set方法
- (void)setAddress:(NSString *)address {
    objc_setAssociatedObject(self, &kHomeAddress, address, OBJC_ASSOCIATION_COPY_NONATOMIC);
}
```

这样就成功添加了一个[分类属性](https://github.com/chenxing640/Runtime/blob/master/RuntimeUsage/RuntimeUsage/Model/YFModel%2BAddingAttr.m)。

Runtime的运用主要包装在`DYFRuntimeWrapper`类中，导入即可快速使用Runtime。欢迎大家查看[Demo](https://github.com/chenxing640/Runtime/tree/master/RuntimeUsage)或者克隆仓库(`git clone https://github.com/chenxing640/Runtime.git`)。
