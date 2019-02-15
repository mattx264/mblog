


Local db connection string
<add name="DefaultConnection" connectionString="Data Source=localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\WebFormsIdentity.mdf;Initial Catalog=WebFormsIdentity;Integrated Security=True"
            providerName="System.Data.SqlClient" />
            For Visual Studio 2015 or higher, replace (localdb)\v11.0 with (localdb)\MSSQLLocalDB in your connection string.
