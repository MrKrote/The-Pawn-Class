# The-Pawn-Class

> Give the Pawn a RootComponent , MeshComponent , Camera
> 
**.H file**
```
UPROPERTY(EditAnywhere, Category = "Mesh")
UStaticMeshComponent* MeshComponent;

UPROPERTY(VisibleAnywhere, Category = "Mesh")
class USphereComponent* SphereComponent;

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
&nbsp;

> FORCEINLINE
> 
**FORCEINLINE - everywhere in the code it will take this function and return MeshComponent.**
FORCEINLINE UStaticMeshComponent* GetMeshComponent() { return MeshComponent; }

&nbsp;

> Create and Attach SphereComponent , MeshComponent , set StaticMesh in C++
> 
**.CPP**
**Constructor**
```
RootComponent = CreateDefaultSubobject<USceneComponent>(TEXT("RootComponent"));
 
SphereComponent = CreateDefaultSubobject<USphereComponent>(TEXT("SphereComponent"));
SphereComponent->SetupAttachment(GetRootComponent());
SphereComponent->InitSphereRadius(40.0f);
SphereComponent->SetCollisionProfileName(TEXT("Pawn"));
MeshComponent = CreateDefaultSubobject<UStaticMeshComponent>(TEXT("MeshComponent"));
MeshComponent->SetupAttachment(GetRootComponent());
static ConstructorHelpers::FObjectFinder<UStaticMesh> MeshComponentAsset(TEXT("StaticMesh'/Game/StarterContent/Shapes/Shape_Sphere.Shape_Sphere'"));
 
if (MeshComponentAsset.Succeeded()) 
{
MeshComponent->SetStaticMesh(MeshComponentAsset.Object);
MeshComponent->SetRelativeLocation(FVector(0.0f, 0.0f, -40.0f));
MeshComponent->SetWorldScale3D(FVector(0.8f, 0.8f, 0.8f));
}
```

> Create Camera , SpringArm
> 
**.H**
```
FORCEINLINE UCameraComponent* GetCameraComponent() { return Camera; }
FORCEINLINE void SetMeshComponent(UCameraComponent* InCamera) { Camera = InCamera; }
 
FORCEINLINE USpringArmComponent* GetSpringArmComponent() { return SpringArm; }
FORCEINLINE void SetSphereComponent(USpringArmComponent* InSpringArm) { SpringArm = InSpringArm; }
```
**.CPP**
**Constructor**
```
SpringArm = CreateDefaultSubobject<USpringArmComponent>(TEXT("SpringArm"));
SpringArm->SetupAttachment(GetRootComponent());
SpringArm->SetRelativeRotation(FRotator(-45.0f, 0.0f, 0.0f));
SpringArm->TargetArmLength = 400.0f;
SpringArm->bEnableCameraLag = true;
SpringArm->CameraLagSpeed = 3.0f;
 
Camera = CreateDefaultSubobject<UCameraComponent>(TEXT("Camera"));
Camera->SetupAttachment(SpringArm,USpringArmComponent::SocketName);
```

&nbsp;

> Create Pawn Camera Rotation
> 

**.H**
```
void PitchCamera(float AxisValue);
void YawCamera(float AxisValue);
FVector2D CameraInput;
```
**.CPP**
**Constructor**
```
CameraInput = FVector2D(0.0f, 0.0f); 
Tick
{
FRotator NewRotation = GetActorRotation();
NewRotation.Yaw += CameraInput.X;
SetActorRotation(NewRotation);
 
FRotator NewSpringArmRotation = SpringArm->GetComponentRotation();
NewSpringArmRotation.Pitch = FMath::Clamp(NewSpringArmRotation.Pitch += CameraInput.Y, -80.0f, -15.0f);
SpringArm->SetWorldRotation(NewSpringArmRotation);
}
SetupPlayerInputComponent
{
PlayerInputComponent->BindAxis(TEXT("CameraPitch"), this, &ACollider::PitchCamera);
PlayerInputComponent->BindAxis(TEXT("CameraYaw"), this, &ACollider::YawCamera);
}
PitchCamera(float AxisValue)
{
CameraInput.Y = AxisValue;
}
YawCamera(float AxisValue)
{
CameraInput.X = AxisValue;
}
```
