@page "/"

<h1>C# Web Playground</h1>

<textarea @bind="UserCode" rows="15" cols="80">// Write your C# code here
Console.WriteLine("Hello, Blazor!");
</textarea>

<br />

<button @onclick="RunCode">Run Code</button>

<pre>@Output</pre>

@code {
    private string UserCode = @"Console.WriteLine(""Hello, Blazor!"");";
    private string Output = "";

    private async Task RunCode()
    {
        var writer = new StringWriter();
        var originalOut = Console.Out;
        Console.SetOut(writer);

        try
        {
            await Microsoft.CodeAnalysis.CSharp.Scripting.CSharpScript.EvaluateAsync(UserCode,
                Microsoft.CodeAnalysis.Scripting.ScriptOptions.Default
                .WithImports("System", "System.Linq", "System.Collections.Generic"));
        }
        catch (Exception ex)
        {
            writer.WriteLine("Error: " + ex.Message);
        }
        finally
        {
            Console.SetOut(originalOut);
        }

        Output = writer.ToString();
    }
}