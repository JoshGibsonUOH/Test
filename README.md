<body>
  <h1>C# Web Playground</h1>
  <textarea id="codeInput" rows="15" cols="80">// Write your C# code here
Console.WriteLine("Hello from the browser!");
</textarea>
  <br />
  <button onclick="runCode()">Run Code</button>
  <pre id="output"></pre>

  <script>
    async function runCode() {
      const code = document.getElementById("codeInput").value;
      const output = await DotNet.invokeMethodAsync("CSharpTest.Wasm", "RunCSharpCode", code);
      document.getElementById("output").innerText = output;
    }
  </script>
</body>


using Microsoft.JSInterop;
using Microsoft.CodeAnalysis.CSharp.Scripting;
using Microsoft.CodeAnalysis.Scripting;
using System.IO;

public partial class Program
{
    [JSInvokable("RunCSharpCode")]
    public static async Task<string> RunCSharpCode(string code)
    {
        var writer = new StringWriter();
        var originalOut = Console.Out;
        Console.SetOut(writer);

        try
        {
            await CSharpScript.EvaluateAsync(code, ScriptOptions.Default
                .WithImports("System", "System.Linq", "System.Collections.Generic")
            );
        }
        catch (Exception ex)
        {
            writer.WriteLine("Error: " + ex.Message);
        }
        finally
        {
            Console.SetOut(originalOut);
        }

        return writer.ToString();
    }
}


dotnet add package Microsoft.CodeAnalysis.CSharp.Scripting --version 4.8.0