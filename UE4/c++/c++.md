# c++



![QQ图片20210930155044](F:\MyNotes\UE4\c++\QQ图片20210930155044.png)





每次在ue4当中新建一个C++类的时候，vs中会生成两个文件。一个是cpp，另一个是h

cpp就是编写游戏逻辑的地方。

h是用来引入新变量的地方。



在引入变量的时候，需要在上面加个类似java当中注解的东西

```c++
UPROPERTY(***)
FVector InitLocation
```

这是用来标识ue4变量的东西，可以选择这个东西在ue4界面当中是否可以编辑以及在哪编辑。

有以下几种方式：

VisibleAnywhere 在哪都能看

EditAnywhere	在哪都能编辑（蓝图当中以及细节面板当中）



VisibleInstanceOnly 只能在细节面板当中看

EditInstanceOnly 只能在细节面板当中编辑



EditDefaultsOnly 只能在蓝图当中编辑

VisibleDefaultsOnly 只能蓝图当中看



后面可以加个Category，这是用来表示ue4当中的编辑界面。

例如 UPROPERTY(EditInstanceOnly, Category = "My Actor Properties | Vector")

![QQ图片20210929165612](F:\MyNotes\UE4\c++\QQ图片20210929165612.png)



https://docs.unrealengine.com/4.26/zh-CN/ProgrammingAndScripting/GameplayArchitecture/Properties/

```c++
	// 注意category中的名称最好用空格来写，不然显示不出来
	UPROPERTY(VisibleAnywhere, Category = "My Actor Components")
	UStaticMeshComponent* MyStaticMesh;

	UPROPERTY(EditInstanceOnly, Category = "My Actor Properties | Vector")
	FVector InitLocation;

	UPROPERTY(VisibleInstanceOnly, Category = "My Actor Properties | Vector")
	FVector PlacedLocation;

	UPROPERTY(EditDefaultsOnly, Category = "My Actor Properties | Vector")
	bool bGotoInitLocation;

	UPROPERTY(VisibleDefaultsOnly, Category = "My Actor Properties | Vector")
	FVector WorldOrigin;

	UPROPERTY(EditAnywhere, Category = "My Actor Properties | Vector", meta = (ClampMin = -5.0f, ClampMax = 5.0f, UIMin = -5.0f, UIMax = 5.0f))
	FVector TickLocationOffset;

	UPROPERTY(EditAnywhere, Category = "My Actor Properties | Vector")
	bool bShouldMove;
```





变量引入好之后，需要在构造函数当中来赋储值。

然后可以在下面的函数当中使用。



UE4 c++的变量名称有要求

1. 派生自Actor的类带有A前缀，如Acontroller
2. 派生自Object的类带有U前缀，如UComponent
3. Enums的前缀是E，如EFortificationType
4. Interface的前缀通常是I，如IAbilitySystemInterface
5. Template的前缀是T，如TArray
6. 派生自SWidget的类（Slate UI）带有前缀S，如SButton
7. 其他类的前缀为字母F，如FVector

