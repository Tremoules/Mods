## What it is

Other mods can communicate with Helpful Additions in order for their content to appear within its various features, such as in the round info screen.

## Functions available

* AddCustomBloon\
This function allows other modders to add custom bloon graphics, such that they show up within Helpful Additions.
```csharp
/// <summary>This function allows other modders to add custom bloon graphics in the form of bytes from an image file.</summary>
/// <param name="bloonId">The id of the custom bloon being added</param>
/// <param name="icon">The icon of the custom bloon being added as bytes from an image file</param>
/// <param name="edge">The graphic for the ends of a timespan for the custom bloon as bytes from an image file</param>
/// <param name="span">The graphic for the span of a timespan for the custom bloon as bytes from an image file</param>
/// <param name="iconSize">The size of the icon in Unity, where the image size is the original, and 200 is the maximum recommended size</param>
public static void AddCustomBloon(string bloonId, byte[] icon, byte[] edge, byte[] span, Vector2? iconSize = null);

/// <summary>This function allows other modders to add custom bloon graphics in the form of a Texture2D.</summary>
/// <param name="bloonId">The id of the custom bloon being added</param>
/// <param name="icon">The icon of the custom bloon being added as a Texture2D</param>
/// <param name="edge">The graphic for the ends of a timespan for the custom bloon as a Texture2D</param>
/// <param name="span">The graphic for the span of a timespan for the custom bloon as a Texture2D</param>
/// <param name="iconSize">The size of the icon in Unity, where the image size is the original, and 200 is the maximum recommended size</param>
public static void AddCustomBloon(string bloonId, Texture2D icon, Texture2D edge, Texture2D span, Vector2? iconSize = null);
```

## How to set it up

In order to interface with Helpful Additions without adding it as a dependency, C# Reflection is required.

The following code is an example of how to set up all available functions for use in your own mod.
Feel free to copy and edit it such that it fits your mod and coding style.

```csharp
public class YourMod : MelonMod {
  private static MethodInfo AddCustomBloonByBytes = null;
  private static MethodInfo AddCustomBloonByTexture2D = null;

  public override void OnApplicationStart() {
      // Gets all loaded assemblies
      Assembly[] assemblies = System.AppDomain.CurrentDomain.GetAssemblies();
      // Find Helpful Addtions
      Assembly helpfulAdditions = assemblies.FirstOrDefault(assembly => assembly.GetName().Name.Equals("Helpful Additions"));
      // Get the Mod class in Helpful Addtions
      System.Type mod = helpfulAdditions?.GetType("HelpfulAdditions.Mod");
      // Get the AddCustomBloon method in Mod
      AddCustomBloonByBytes = mod?.GetMethod("AddCustomBloon", new System.Type[] {
          typeof(string), typeof(byte[]), typeof(byte[]), typeof(byte[]), typeof(Vector2?)
      });
      AddCustomBloonByTexture2D = mod?.GetMethod("AddCustomBloon", new System.Type[] {
          typeof(string), typeof(Texture2D), typeof(Texture2D), typeof(Texture2D), typeof(Vector2?)
      });
  }

  private static void AddCustomBloon(string bloonId, byte[] icon, byte[] edge, byte[] span, Vector2? iconSize = null) =>
      AddCustomBloonByBytes?.Invoke(null, new object[] { bloonId, icon, edge, span, iconSize });

  private static void AddCustomBloon(string bloonId, Texture2D icon, Texture2D edge, Texture2D span, Vector2? iconSize = null) =>
      AddCustomBloonByTexture2D?.Invoke(null, new object[] { bloonId, icon, edge, span, iconSize });
}
```
Note: I make use of the `?.` operator as my choice of null check. Make sure to include some kind of null check wherever I use it.
