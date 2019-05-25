# **Commands**

Commands can be used for easy communication with your gamemode. RageMP.Net supports command handling including type parsing.
There are multiple ways to register commands in RageMP.Net.

## Command Handlers

Command Handlers are an easy way to register new commands. A command handler is a simple class that implements [ICommandHandler](~/api/AlternateLife.RageMP.Net.Interfaces.ICommandHandler.yml) and contains a collection of methods with the attribute [Command](~/api/AlternateLife.RageMP.Net.Attributes.CommandAttribute.yml). 

#### Registration

You can register new command handlers by calling following method:

```cs
public class Main implements IResource {

    public Main() 
    {
        MP.Commands.RegisterHandler(new CommandHandler());  
    }

    public Task OnStartAsync()
    {
        return Task.CompletedTask;
    }

    public Task OnStopAsync()
    {
        return Task.CompletedTask;
    }

}
```

#### Signature

The first parameter of a command must always be `IPlayer`, the player that issued the command. From there commands within a command handler class support the following parameter types:

- All primitive types
- Strings
- Enums

Alternatively you can also use the `IPlayer player, string[] arguments` signature within command handlers.

Since all commands will be executed asynchronously, the return type must be `Task`. 

#### Example

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

## Delegate Commands

Delegate commands are an alternative way to add or register commands without the need of a command handler. The only downside of this way to register commands is, that you are unable to make use of our integrated type-parser.

#### Registration

```cs
public class Main implements IResource {

    public Main() 
    {
        // Register command with class method
        MP.Commands.Register("test1", ExampleCommand);

        // Register command with lambda method
        MP.Commands.Register("test", async (player, arguments) => {
            await player.OutputChatBoxAsync("Command with lambda successful!");
        });
    }

    private async Task ExampleCommand(IPlayer player, string[] arguments) 
    {
        await player.OutputChatBoxAsync("Command with method successful!");
    }

    public Task OnStartAsync()
    {
        return Task.CompletedTask;
    }

    public Task OnStopAsync()
    {
        return Task.CompletedTask;
    }

}
```

## Error Handling

Whenever a command is not found or type parsing fails, RageMP.Net allows you to define how to handle this situation. By subscribing to `MP.Events.PlayerCommandFailed` you will be notified with an error message, reason, the players input and the player issuing the command.

## Removing Commands

Using `MP.Commands.Remove()` and `MP.Commands.RemoveHandler()`, for commands and command handlers respectively, you can remove any commands at runtime.