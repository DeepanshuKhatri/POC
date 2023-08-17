# Microservices in Nest.js Application

## Step 1: Create two Nest.js Applications
One will be the main server and the other application will be treated as a microservice.

## Step 2: Installation of required packages in both nest applications
     npm i @nestjs/microservices

## Step 3: In Microservice Application, do the following changes:

### 1.> In main.ts of microservice, add the following code:
    const app = await NestFactory.createMicroservice<MicroserviceOptions>(
          AppModule,
          {
            transport: Transport.TCP
          }
        }
        app.listen();
  Note: Transport and MicroserviceOptions are imported from ``@nestjs/microservices``

### 2.> In app.controller.ts, add the following code:
    @EventPattern('user_created')
    handleUserCreated(data:CreateUserEvent){
      console.log('handleUserCreate-communication', data);
    }


## Step3: In the main server Nest Application where we will use this microservice,

### 1.> Create a create-user-event.ts file inside ``src`` folder and paste following code into it:
    export class createUserEvent {
      constructor( public readonly email:String ){}
    }

### 2.> Create a create-user-request.dto.ts
    export class CreateUserRequest{
      email:String;
      password: String;
    }

### 3.> Update imports in app.module.ts
    imports: [
      ClientsModule.register([
        {
          name:'COMMUNICATION',
          transport: Transport.TCP
        }
      ])
    ],
  Note: Transport and ClientsModule is imported from @nestjs/microservices

### 4.> In app.controller.ts
    @Post()
    createUser(@Body() createUserRequest:CREAteUserRequest){
      return this.appService.createUser(createUserRequest)
    }

### 5.> In app.service.ts
    private readonly users: any[] = [] // This is to store all users data
    constructor(@Inject('COMMUNICATION') private readonly communicationClient: ClientProxy){]

    createUser(createUserRequest:CReateUserRequest){
      this.users.push(createUserRequest);
      this.communicationClient.emit('user_created', new createUserEvent(createUserRequest.email))
    }
        



###janmes





