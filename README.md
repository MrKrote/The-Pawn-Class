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

&nbsp;

> Setting PlayerInput Moving
> 
**.H**
```
  UPROPERTY(EditAnywhere, Category = "PawnMovement")
  float MaxSpeed;
private:
  void MoveForward(float Value);
  void MoveRight(float Value);
  FVector CurrentVelocity;
```
**.CPP**
**Constructor**
```
CurrentVelocity = FVector(0.0f,0.0f,0.0f);
MaxSpeed = 100.0f;
```
```
void ACritter::SetupPlayerInputComponent(UInputComponent* PlayerInputComponent)
{
Super::SetupPlayerInputComponent(PlayerInputComponent);	
PlayerInputComponent->BindAxis(TEXT("MoveForward"),this,&ACritter::MoveForward);
PlayerInputComponent->BindAxis(TEXT("MoveRight"), this, &ACritter::MoveRight);
}
void ACritter::MoveForward(float Value) 
{
CurrentVelocity.X = FMath::Clamp(Value, -1.f, 1.f) * MaxSpeed;
}
void ACritter::MoveRight(float Value)
{
CurrentVelocity.Y = FMath::Clamp(Value, -1.f, 1.f) * MaxSpeed;
}
```
**Tick()**
```
FVector NewLocation = GetActorLocation() + (CurrentVelocity * DeltaTime);
SetActorLocation(NewLocation);
```
