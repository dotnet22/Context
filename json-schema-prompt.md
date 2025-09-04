# GOALS
- This application has a method that reads a json file `configuration.json` file and deserialize the data into UserConfiguration
- The user of the code generator will create a configuration.json file using VSCode and fill the configurations that code generator will consider while generating the code.
- When the user prepares configuration.json, I want user to have a nice Intellisense feature, so he won't have to remember column/table names while writing the file.
- Intellisense is possible using json schmemas.
- I want a method to generate as many schemas as might be required to provide user with nice intellisense.

public class UserConfiguration
{
    public Dictionary<string, TableAppConfiguration> Tables { get; set; } = new();
}

public class TableAppConfiguration
{
    public int PermissionStart { get; set; }
    public FormConfiguration? Form { get; set; }
    public ListConfiguration? List { get; set; }
    public ViewConfiguration? View { get; set; }
    public string? Lookup { get; set; }
}

public class FormConfiguration
{
    public Dictionary<string, FieldConfiguration> Fields { get; set; } = new();
    public LayoutConfiguration? Layout { get; set; }
    public List<string> ExcludedFields { get; set; } = new();
}

public class ListConfiguration
{
    public Dictionary<string, FieldConfiguration?> Fields { get; set; } = new();
    public List<string> ExcludedFields { get; set; } = new();
    public FilterConfiguration? Filter { get; set; }
}

public class ViewConfiguration
{
    public List<ViewSectionConfiguration> Sections { get; set; } = new();
    public List<string> ExcludedFields { get; set; } = new();
    public LayoutConfiguration? Layout { get; set; }
}

public class ViewSectionConfiguration
{
    public string? Title { get; set; }
    public Dictionary<string, FieldConfiguration> Fields { get; set; } = new();
}

public class FieldConfiguration
{
    public string? Sort { get; set; }
    public ReferencedField? DependsOn { get; set; }
    public FieldSize? Size { get; set; }
    public string? Format { get; set; }
    public FieldType? FieldType { get; set; }
}

public class LayoutConfiguration
{
    public LayoutType Type { get; set; }
    public string? Columns { get; set; }
}

public enum LayoutType
{
    Horizontal,
    Vertical
}


public enum FieldType
{
    Select,
    AsyncSelect,
    SelectCascade,
    MultiSelect,
    AsyncMultiSelect,
    Password,
    Text,
    Date,
    Time,
    Checkbox,
    CheckboxGroup,
    Radio,
    RadioGroup,
    Component
}


public class FieldSize
{
    public string? XS { get; set; }
    public string? SM { get; set; }
    public string? MD { get; set; }
    public string? LG { get; set; }
    public string? XL { get; set; }
}

public class ReferencedField
{
    public string TableName { get; set; }
    public string FieldName { get; set; }
}


