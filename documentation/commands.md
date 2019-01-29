# **Commands**

Commands can be used for easy communication with your gamemode. RageMP.Net supports command handling including type parsing.
There are multiple ways to register commands in RageMP.Net.

## Command Handlers

Commands can be created in handler classes, implementing from [ICommandHandler](~/api/AlternateLife.RageMP.Net.Interfaces.ICommandHandler.yml).
Within these classes you can declare methods and add the [Command](~/api/AlternateLife.RageMP.Net.Attributes.CommandAttribute.yml) attribute them to mark them as a command. The attribute will require you to give the command a name.

### Handler Registration

You can register new command handlers by calling following method:

```cs
MP.Commands.RegisterHandler(new CommandHandler());  
```

## Delegate Commands

You can also add single commands without the need of handlers.
Those commands are restricted to the `IPlayer string[]` signature and do not support type parsing.

### Command Registration

Delegate commands can be registered by calling the following method:

```cs
MP.Commands.Register(TestCommand);
```

## Command Signature

The first parameter of a command must always be `IPlayer`, the player that issued the command. From there commands within a command handler class support the following parameter types:

- All primitive types
- Strings
- Enums

Alternatively you can also use the `IPlayer string[]` signature within command handlers.

Since all commands will be executed asynchronously, the return type must be `Task`. 

## Example Commands

Let's define a simple command for creating a vehicle within a command handler:

```cs
public class CommandHandler : ICommandHandler
{
    [Command("vehicle")]
    public async Task Vehicle(IPlayer player, VehicleHash vehicleName)
    {
        var vehicle = await MP.Vehicles.NewAsync(vehicleName, player.Position);

        player.PutIntoVehicle(vehicle, -1);

        await player.OutputChatBoxAsync("Vehicle created");
    }
}
```

This command could be called by typing `/vehicle T20` into the ingame chat.

More example commands can be found in the example resource.

## Error Handling

Whenever a command is not found or type parsing fails, RageMP.Net allows you to define how to handle this situation. By subscribing to `MP.Commands.CommandError` you will be notified with an error message, reason and the player issuing the command.

## Removing Commands

Using `MP.Commands.Remove()` and `MP.Commands.RemoveHandler()`, for commands and command handlers respectively, you can remove any commands at runtime.