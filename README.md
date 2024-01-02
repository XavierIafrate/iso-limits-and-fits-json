# iso-limits-and-fits-json

## ISO System of Limits and Fits (Tolerances and Deviations) - JSON Serialised

I have serialised the tables found in `hole-shaft-tolerance-chart.pdf` to a structure in the below format - I am not aware of any errors but cannot guarantee anything.

### Sample C# Class

``` csharp
    public class StandardFit
    {
        public string Symbol { get; set; }
        public List<ToleranceZone> ToleranceZones { get; set; }

        public ToleranceZone At(double nominal)
        {
            return ToleranceZones.Find(x => nominal > x.Over && nominal <= x.To);
        }
    }

    public class ToleranceZone
    {
        public int Over { get; set; }
        public int To { get; set; }
        public double? Min { get; set; }
        public double? Max { get; set; }
    }
```

### Example Usage

``` csharp
 string fitsJson = File.ReadAllText("fits.txt");
 List<StandardFit> fits =
  Newtonsoft.Json.JsonConvert.DeserializeObject<List<StandardFit>>(fitsJson);

 StandardFit h12 = fits.Find(x => x.Symbol == "h12");

 // At basic 20mm
 ToleranceZone result = h12.At(20);

 double max = result.Max ?? throw new Exception("undefined in this zone"); // +0.000
 double min = result.Min ?? throw new Exception("undefined in this zone"); // -0.210

// Therefore result is 20.000 +0.000 / -0.210
```

