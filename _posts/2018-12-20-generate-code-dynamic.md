```CSHARP
using System;
using System.CodeDom;
using System.CodeDom.Compiler;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Reflection;
using System.Runtime.Loader;
using System.Text;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.CodeAnalysis;
using Microsoft.CodeAnalysis.CSharp;
using Microsoft.CodeAnalysis.Emit;
using Microsoft.CSharp;

namespace WebService.Pages
{
    public class IndexModel : PageModel
    {
        public void OnGet()
        {
           
           getMeCOde();
          //  assembly
            //Type fooType = assembly.GetType("Foo");
           // MethodInfo printMethod = fooType.GetMethod("Print");
          //  object foo = assembly.CreateInstance("Foo");
         //   printMethod.Invoke(foo, BindingFlags.InvokeMethod, null, null, CultureInfo.CurrentCulture);
        }
       
        private void getMeCOde()
        {
            string codeToCompile = @"
            using System;
            namespace RoslynCompileSample
            {
                public class Writer
                {
                    public string Write(string message)
                    {
return ""czesc"";
                         //Console.WriteLine(""you said '""+message+""!'"");
                    }
                }
            }";
          
            // Write("Parsing the code into the SyntaxTree");
            SyntaxTree syntaxTree = CSharpSyntaxTree.ParseText(codeToCompile);

            string assemblyName = Path.GetRandomFileName();
            MetadataReference[] references = new MetadataReference[]
            {
                MetadataReference.CreateFromFile(typeof(object).GetTypeInfo().Assembly.Location)
            };

           // Write("Compiling ...");
            CSharpCompilation compilation = CSharpCompilation.Create(
                assemblyName,
                syntaxTrees: new[] { syntaxTree },
                references: references,
                options: new CSharpCompilationOptions(OutputKind.DynamicallyLinkedLibrary));

            using (var ms = new MemoryStream())
            {
                EmitResult result = compilation.Emit(ms);

                if (!result.Success)
                {
                   // Write("Compilation failed!");
                    IEnumerable<Diagnostic> failures = result.Diagnostics.Where(diagnostic =>
                        diagnostic.IsWarningAsError ||
                        diagnostic.Severity == DiagnosticSeverity.Error);

                    foreach (Diagnostic diagnostic in failures)
                    {
                        Console.Error.WriteLine("\t{0}: {1}", diagnostic.Id, diagnostic.GetMessage());
                    }
                }
                else
                {
                  //  Write("Compilation successful! Now instantiating and executing the code ...");
                    ms.Seek(0, SeekOrigin.Begin);

                    Assembly assembly = AssemblyLoadContext.Default.LoadFromStream(ms);
                    var type = assembly.GetType("RoslynCompileSample.Writer");
                    var instance = assembly.CreateInstance("RoslynCompileSample.Writer");
                    var meth = type.GetMember("Write").First() as MethodInfo;
                    var r =meth.Invoke(instance, new[] { "joel" });
                }
            }
        }
    }
}
