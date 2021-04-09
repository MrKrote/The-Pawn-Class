# The-Pawn-Class

> Give the Pawn a RootComponent , MeshComponent , Camera
> 
**.H file**
```
UPROPERTY(EditAnywhere, Category = "Mesh")
UStaticMeshComponent* MeshComponent;

UPROPERTY(EditAnywhere)
class UCameraComponent* Camera;

//RootComponent doesn`t need a decleration
```
**.CPP**
**Constructor**
```
RootComponent = CreateDefaultSubobject<USceneComponent>(TEXT("RootComponent"));
MeshComponent = CreateDefaultSubobject<UStaticMeshComponent>(TEXT("MeshComponent"));
MeshComponent->SetupAttachment(GetRootComponent());
 
Camera = CreateDefaultSubobject<UCameraComponent>(TEXT("Camera"));
Camera->SetupAttachment(GetRootComponent());
 
Camera->SetRelativeLocation(FVector(-300.0f, 0.0f, 300.0f));
Camera->SetRelativeRotation(FRotator(-45.0f, 0.0f, 0.0f));
GameMode_BP -> Default Pawn Class
```

WorldSettings -> GameMode Override
```
AutoPossessPlayer = EAutoReceiveInput::Player0; // default gamemode 
```
