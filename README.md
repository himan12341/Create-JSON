# Create-JSON
codeunit 50254 CreatedJson
{
    procedure ExportCustomerJson(Customer: Record Customer)
    var
        TempBlob: Codeunit "Temp Blob";
        CustomerJson: JsonObject;
        CustomerOutStream: OutStream;
        CustomerInStream: InStream;
        ExportFileName: text;
    begin
        Customer.SetAutoCalcFields("Balance (LCY)", "Sales (LCY)");
        CustomerJson.Add(Customer.FieldCaption("No."), Customer."No.");
        CustomerJson.Add(Customer.FieldCaption(Name), Customer.Name);
        CustomerJson.Add(Customer.FieldCaption("Balance (LCY)"), Customer."Balance (LCY)");
        CustomerJson.Add(Customer.FieldCaption("Sales (LCY)"), Customer."Sales (LCY)");

        CustomerJson.Add('ShiptoAddress', GetShiptoAddressForCustomer(Customer."No."));

        TempBlob.CreateOutStream(CustomerOutStream);
        if CustomerJson.WriteTo(CustomerOutStream) then begin

            ExportFileName := 'Customer' + Customer."No." + '.json';
            TempBlob.CreateInStream(CustomerInStream);
            DownloadFromStream(CustomerInStream, '', '', '', ExportFileName);
        end;


    end;

    local procedure GetShiptoAddressForCustomer(No: Code[20]) ShipToAddressArray: JsonArray
    var
        ShipToAddress: Record "Ship-to Address";
        ShipToAddressJson: JsonObject;
    begin
        ShipToAddress.SetRange("Customer No.", No);
        if ShipToAddress.FindSet() then
            repeat
                Clear(ShipToAddressJson);
                ShipToAddressJson.Add(ShipToAddress.FieldCaption(Code), ShipToAddress.Code);
                ShipToAddressJson.Add(ShipToAddress.FieldCaption(Name), ShipToAddress.Name);
                ShipToAddressJson.Add(ShipToAddress.FieldCaption(City), ShipToAddress.City);
                ShipToAddressArray.Add(ShipToAddressJson);
            until (ShipToAddress.Next() = 0);
    end;
}
