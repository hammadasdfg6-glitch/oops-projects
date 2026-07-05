#include<iostream>
#include<string>
#include<vector>
#include<iomanip>
#include<conio.h>
#include<fstream>
#include<exception>
using namespace std;
class product{
    public:
        string name;
        string category;
        int id;
        static int nextid;
        int qty,iqty;
        float price,stck;
    public:
        static vector<product>list;
        product(){
            name = "";
            category = "";
            qty = 0;
            iqty = 0;
            price = 0;
            stck = 0;
        }
       static void loadfromfile(){
            ifstream fin("products.txt");
    if (fin) {
        product temp;
        while (getline(fin, temp.name)) {
            getline(fin, temp.category);
            fin >> temp.id;
            fin >> temp.qty;
            fin >> temp.iqty;
            fin >> temp.price;
            fin >> temp.stck;
            fin.ignore();
            if (temp.id > nextid) {
                nextid = temp.id;
            }
            list.push_back(temp);
        }
        fin.close();
    }
        }
        product(string name,string category,int qty, float price){
            this->name=name;
            this->category=category;
            ++nextid;
            id=nextid;
            this->qty=qty;
            this->price=price;
            iqty=qty;
            stck=100;
        }
        static void savetofile() {
    ofstream fout("products.txt");
    for (int i = 0; i < list.size(); i++) {
        fout << list[i].name << endl;
        fout << list[i].category << endl;
        fout << list[i].id << endl;
        fout << list[i].qty << endl;
        fout << list[i].iqty << endl;
        fout << list[i].price << endl;
        fout << list[i].stck << endl;
    }
    fout.close();
}
};
int product::nextid=0;
vector<product>product::list;
class billing;
class inventory:public product{
    private:
        int i=-1;
    public:
        inventory(){
            while (i!=6)
            {
                system("cls");
                cout<<"================================="<<endl;
                cout<<"        INVENTORY SYSTEM         "<<endl;
                cout<<"================================="<<endl;
                cout<<"1. Add Products"<<endl;
                cout<<"2. Update Products"<<endl;
                cout<<"3. Delete Products"<<endl;
                cout<<"4. View Products"<<endl;
                cout<<"5. Stock level"<<endl;
                cout<<"6. Exit"<<endl;
                cout<<"---------------------------------"<<endl;
                cout<<"Choose options: ";
                cin>>i;
                switch(i){
                    case 1:
                    Add_product();
                    break;
                    case 2:
                    Update_product();
                    break;
                    case 3:
                    Delete_product();
                    break;
                    case 4:
                    View_product();
                    break;
                    case 5:
                    Stck_lvl();
                    break;
                    case 6:
                    break;
                    default:
                    cout<<endl<<"Invalid Choice!";
                }
            }
        }
        void Add_product(){
            system("cls");
            char n;
            do
            {
                system("cls");
                string name,category;
                int qty;
                float price;
                cout<<"----- ADD NEW PRODUCT -----"<<endl;
                cout<<endl<<"Enter name: ";
                cin.ignore();
                getline(cin,name);
                cout<<endl<<"Enter category: ";
                getline(cin,category);
                cout<<endl<<"Enter quantity: ";
                cin>>qty;
                cout<<endl<<"Enter price: $";
                cin>>price;
                list.push_back(product(name,category,qty,price));
                cout<<endl<<"Do you want to add another product?"<<endl<<"Press 'Y'/'y' to add another product: ";
                cin>>n;
            }while (n=='Y'||n=='y');
        }
        static void View_product(bool pause=true){
            system("cls");
            cout<<"================================================================="<<endl;
            cout<<left<<setw(10)<<"ID"<<setw(20)<<"Name"<<setw(15)<<"Price"<<setw(10)<<"Qty"<<endl;
            cout<<"-----------------------------------------------------------------"<<endl;
            for(int k=0;k<list.size();k++){
                cout<<left<<setw(10)<<list[k].id<<setw(20)<<list[k].name<<"$"<<setw(14)<<list[k].price<<setw(10)<<list[k].qty<<endl;
            }
            cout<<"================================================================="<<endl;
            if(pause){cout<<"Press any key...";getch();}
        }
        void Update_product(){
            system("cls");
            View_product(false);
            cout<<endl<<"Enter id to update: ";
            cin>>i;
            bool found = false;
            for(int k=0;k<list.size();k++){
                if (i==list[k].id)
                {
                    found = true;
                    cout<<endl<<"Enter new name: ";
                    cin.ignore();
                    getline(cin,list[k].name);
                    cout<<endl<<"Enter new category: ";
                    getline(cin,list[k].category);
                    cout<<endl<<"Enter new price: $";
                    cin>>list[k].price;
                    cout<<endl<<"Enter new quantity: ";
                    cin>>list[k].qty;
                    list[k].iqty=list[k].qty;
                    list[k].stck=100;
                    cout<<endl<<"Product updated Successfully...";
                    break;
                }
            }
            if(!found) cout<<endl<<"Product ID not found!";
            getch();
        }
        void Delete_product(){
            system("cls");
            View_product(false);
            cout<<endl<<"Enter id to delete: ";
            cin>>i;
            bool found = false;
            for (int k = 0;  k<list.size(); k++)
            {
                if(i==list[k].id){
                    found = true;
                    list.erase(list.begin()+k);
                    cout<<endl<<"Product Deleted successfully...";
                    break;
                }
            }
            try
            {
                if(!found)
                {
                    throw runtime_error("Product ID not found!");
                }
            }
            catch(const std::exception& e)
            {
                cout<<endl<<"Error: "<<e.what()<<endl;
            }
            getch();
        }
        void Stck_lvl(){
            system("cls");
            cout<<"=================================================="<<endl;
            cout<<left<<setw(10)<<"SrNo."<<setw(20)<<"Name"<<setw(15)<<"Stock lvl"<<endl;
            cout<<"--------------------------------------------------"<<endl;
            for(int i=0;i<list.size();i++){
                cout<<left<<setw(10)<<list[i].id<<setw(20)<<list[i].name<<fixed<<setprecision(2)<<list[i].stck<<"%"<<endl;
            }
            cout<<"=================================================="<<endl;
            cout<<"Press any key...";
            getch();
        }
friend class billing;
};
class sales{
    private:
        struct SoldItem{
            int id;
            string name;
            int qtySold;
        };
        struct SaleRecord{
            string date;
            float totalAmount;
            string method;
        };
        static string currentDate;
        static vector<SoldItem> lifetimeStats;
        static vector<SaleRecord> history;
    public:
        static void loadfromfile(){
            ifstream fin("history.txt");
            if(fin){
                SaleRecord temp;
                while(getline(fin, temp.date)){
                    fin>>temp.totalAmount;
                    fin.ignore();
                    getline(fin, temp.method);
                    history.push_back(temp);
                }
                fin.close();
            }
            ifstream fin2("stats.txt");
            if(fin2){
                getline(fin2, currentDate);
                SoldItem temp2;
                while(fin2>>temp2.id){
                    fin2.ignore();
                    getline(fin2, temp2.name);
                    fin2>>temp2.qtySold;
                    fin2.ignore();
                    lifetimeStats.push_back(temp2);
                }
                fin2.close();
            }
        }
        static void savetofile(){
            ofstream fout("history.txt");
            for(int i=0; i<history.size(); i++){
                fout<<history[i].date<<endl;
                fout<<history[i].totalAmount<<endl;
                fout<<history[i].method<<endl;
            }
            fout.close();
            ofstream fout2("stats.txt");
            fout2<<currentDate<<endl;
            for(int i=0; i<lifetimeStats.size(); i++){
                fout2<<lifetimeStats[i].id<<endl;
                fout2<<lifetimeStats[i].name<<endl;
                fout2<<lifetimeStats[i].qtySold<<endl;
            }
            fout2.close();
        }
        static void updateDate(){
            system("cls");
            cout<<"----- UPDATE SYSTEM DATE -----"<<endl;
            cout<<"Current Date: "<<currentDate<<endl;
            cout<<"Enter new date: ";
            cin>>currentDate;
            cout<<"Date updated!"<<endl;
            cout<<"Press any key...";
            getch();
        }
        static void recordSale(float total, string method){
            SaleRecord sr;
            sr.date = currentDate;
            sr.totalAmount = total;
            sr.method = method;
            history.push_back(sr);
        }
        static void recordItemSold(int id, string name, int qty){
            bool found = false;
            for(int i=0; i<lifetimeStats.size(); i++){
                if(lifetimeStats[i].id == id){
                    lifetimeStats[i].qtySold += qty;
                    found = true;
                    break;
                }
            }
            if(!found){
                SoldItem newItem;
                newItem.id = id;
                newItem.name = name;
                newItem.qtySold = qty;
                lifetimeStats.push_back(newItem);
            }
        }
        static void showHistory(){
            system("cls");
            cout<<"=================================================="<<endl;
            cout<<"                 SALES HISTORY                    "<<endl;
            cout<<"=================================================="<<endl;
            cout<<left<<setw(15)<<"Date"<<setw(15)<<"Method"<<setw(15)<<"Total"<<endl;
            cout<<"--------------------------------------------------"<<endl;
            for(int i=0; i<history.size(); i++){
                cout<<left<<setw(15)<<history[i].date<<setw(15)<<history[i].method<<"$"<<history[i].totalAmount<<endl;
            }
            cout<<"=================================================="<<endl;
            cout<<"Press any key...";
            getch();
        }
        static void showMostSold(){
            system("cls");
            cout<<"=================================================="<<endl;
            cout<<"              MOST SOLD PRODUCTS                  "<<endl;
            cout<<"=================================================="<<endl;
            if(lifetimeStats.empty()){
                cout<<"No data."<<endl;
            }else{
                vector<SoldItem> sorted = lifetimeStats;
                for(int i=0; i<sorted.size(); i++){
                    for(int j=i+1; j<sorted.size(); j++){
                        if(sorted[i].qtySold < sorted[j].qtySold){
                            SoldItem temp = sorted[i];
                            sorted[i] = sorted[j];
                            sorted[j] = temp;
                        }
                    }
                }
                cout<<left<<setw(10)<<"ID"<<setw(20)<<"Name"<<setw(15)<<"Total Sold"<<endl;
                cout<<"--------------------------------------------------"<<endl;
                for(int i=0; i<sorted.size(); i++){
                    cout<<left<<setw(10)<<sorted[i].id<<setw(20)<<sorted[i].name<<setw(15)<<sorted[i].qtySold<<endl;
                }
            }
            cout<<"=================================================="<<endl;
            cout<<"Press any key...";
            getch();
        }
        static void salesMenu(){
            int ch=-1;
            while(ch!=4){
                system("cls");
                cout<<"================================="<<endl;
                cout<<"         SALES DASHBOARD         "<<endl;
                cout<<"================================="<<endl;
                cout<<"Current Date: "<<currentDate<<endl;
                cout<<"---------------------------------"<<endl;
                cout<<"1. Sales History"<<endl;
                cout<<"2. Most Sold Products"<<endl;
                cout<<"3. Update Date"<<endl;
                cout<<"4. Exit"<<endl;
                cout<<"---------------------------------"<<endl;
                cout<<"Choose option: ";
                cin>>ch;
                switch(ch){
                    case 1: showHistory(); break;
                    case 2: showMostSold(); break;
                    case 3: updateDate(); break;
                    case 4: break;
                    default: cout<<"Invalid choice!"; getch();
                }
            }
        }
};
string sales::currentDate = "01-01-2024";
vector<sales::SoldItem> sales::lifetimeStats;
vector<sales::SaleRecord> sales::history;
class payment{
    protected:
    float ammount;
    public:
        payment(float amt){
            ammount=amt;
        }
        virtual float calculateTotal() const = 0;
        virtual ~payment(){};
};
class cash:public payment{
    public:
        cash(float amt):payment(amt){}
        float calculateTotal() const{
            return ammount+(ammount*0.17);
        }
};
class card:public payment{
    public:
        card(float amt):payment(amt){}
        float calculateTotal() const{
            return ammount+(ammount*0.05);
        }
};
class billing:public product {
    protected:
        int id;
        string name;
        string category;
        int qty;
        float price;
    public:
        vector<billing> cart;
        void Billing(){
            int ch=-1;
            while (ch!=5)
            {
                system("cls");
                cout<<"================================="<<endl;
                cout<<"          BILLING MENU           "<<endl;
                cout<<"================================="<<endl;
                cout<<"1. Buy"<<endl;
                cout<<"2. View Cart"<<endl;
                cout<<"3. Print Receipt"<<endl;
                cout<<"4. Check Out"<<endl;
                cout<<"5. Exit"<<endl;
                cout<<"---------------------------------"<<endl;
                cout<<"Enter Choice: ";
                cin>>ch;
                switch(ch){
                    case 1:
                    {Buy();
                    break;}
                    case 2:
                    {View_cart();
                    break;}
                    case 3:
                    Print_Receipt();
                    break;
                    case 4:
                    Checkout();
                    break;
                    case 5:
                    break;
                    default:
                    cout<<"Invalid Choice!"<<endl;
                }
            }
        }
    void Buy(){
    billing temp;
    char ch;
    int input_id, target_idx = -1;
    system("cls");
    do{
        inventory::View_product(false);
        cout<<endl<<"Enter id of product: ";
        cin>>input_id;
        for(int k=0; k<list.size(); k++){
            if(list[k].id == input_id){
                target_idx = k;
                break;
            }
        }
        if (target_idx == -1) {
            cout << endl << "Product ID not found!" << endl;
            getch();
            continue;
        }
        temp.id = input_id;
        cout<<endl<<"Enter quantity: ";
        cin>>temp.qty;
        bool valid_qty = true;
        if(temp.qty > list[target_idx].qty){
            valid_qty = false;
            try
            {
                if(!valid_qty)
                throw runtime_error("Not enough quantity is available!");
            }
            catch(const runtime_error& e){
                cout<<endl<<"Error: "<<e.what()<<endl;
                getch();
                break;
            }
        }
        temp.name = list[target_idx].name;
        temp.category = list[target_idx].category;
        temp.price = (temp.qty * list[target_idx].price);
        list[target_idx].qty -= temp.qty;
        if (list[target_idx].iqty > 0) {
            list[target_idx].stck = (float(list[target_idx].qty) / list[target_idx].iqty) * 100;
        } else {
            list[target_idx].stck = 0;
        }
        cart.push_back(temp);
        cout<<endl<<temp.qty<<"x "<<temp.name<<" added to cart!"<<endl;
        cout<<"Do you want to continue shopping?"<<endl<<"Enter Y/y to continue shopping: ";
        cin>>ch;
    }while(ch=='Y'||ch=='y');
}
        void View_cart(){
            system("cls");
            cout<<"================================================================="<<endl;
            cout<<"                          SHOPPING CART                          "<<endl;
            cout<<"================================================================="<<endl;
            cout<<left<<setw(10)<<"ID"<<setw(20)<<"Name"<<setw(15)<<"Quantity"<<setw(15)<<"Price"<<endl;
            cout<<"-----------------------------------------------------------------"<<endl;
            for(int i=0;i<cart.size();i++){
                cout<<left<<setw(10)<<cart[i].id<<setw(20)<<cart[i].name<<setw(15)<<cart[i].qty<<"$"<<setw(14)<<cart[i].price<<endl;
            }
            cout<<"================================================================="<<endl;
            cout<<"Press any key...";
            getch();
        }
        float total=0;
        void Print_Receipt(){
            total=0;
            system("cls");
            cout<<"========================================================================="<<endl;
            cout<<"                             OFFICIAL RECEIPT                            "<<endl;
            cout<<"========================================================================="<<endl;
            cout<<left<<setw(8)<<"Sr.no"<<setw(20)<<"Name"<<setw(15)<<"Unit Price"<<setw(12)<<"Quantity"<<setw(15)<<"Total"<<endl;
            cout<<"-------------------------------------------------------------------------"<<endl;
            for(int i=0;i<cart.size();i++){
                cout<<left<<setw(8)<<i+1<<setw(20)<<cart[i].name<<"$"<<setw(14)<<(cart[i].price / cart[i].qty)<<setw(12)<<cart[i].qty<<"$"<<setw(14)<<cart[i].price<<endl;
                total+=cart[i].price;
            }
            cout<<"-------------------------------------------------------------------------"<<endl;
            cout<<right<<setw(55)<<"SUB TOTAL: $"<<total<<endl;
            getch();
        }
        void Checkout(){
            payment *p;
            float gtotal;
            system("cls");
            Print_Receipt();
            int n;
            cout<<endl<<"================================="<<endl;
            cout<<"      CHOOSE PAYMENT METHOD      "<<endl;
            cout<<"================================="<<endl;
            cout<<"1. CARD (5% Tax)"<<endl;
            cout<<"2. CASH (17% Tax)"<<endl;
            cout<<"---------------------------------"<<endl;
            cout<<"Enter choice: ";
            cin>>n;
            switch (n)
            {
            case 1:
                {
                    p=new card(total);
                    gtotal=p->calculateTotal();
                    cout<<"-------------------------------------------------------------------------"<<endl;
                    cout<<right<<setw(55)<<"TAX (5%): $"<<(total*0.05)<<endl;
                    cout<<right<<setw(55)<<"GRAND TOTAL: $"<<gtotal<<endl;
                    cout<<"========================================================================="<<endl;
                }
                break;
            case 2:
            {   p=new cash(total);
                gtotal=p->calculateTotal();          
                cout<<"-------------------------------------------------------------------------"<<endl;
                cout<<right<<setw(55)<<"TAX (17%): $"<<(total*0.17)<<endl;
                cout<<right<<setw(55)<<"GRAND TOTAL: $"<<gtotal<<endl;
                cout<<"========================================================================="<<endl;
                break;
            }
            default:
                cout<<endl<<"Invalid Choice!"<<endl;
                break;
            }
            if(n==1 || n==2){
                string method = (n==1) ? "CARD" : "CASH";
                sales::recordSale(gtotal, method);
                for(int i=0; i<cart.size(); i++){
                    sales::recordItemSold(cart[i].id, cart[i].name, cart[i].qty);
                }
                cout<<endl<<"Payment Successful! Thank you for purchasing."<<endl;
                delete p;
                cart.clear(); 
                total=0;
            }
            getch();
        }
};
int main(){
    product::loadfromfile();
    sales::loadfromfile();
    int ch=-1;
    while(ch!=4){
        system("cls");
        cout<<"================================="<<endl;
        cout<<"    STORE MANAGEMENT SYSTEM      "<<endl;
        cout<<"================================="<<endl;
        cout<<"1. Inventory"<<endl;
        cout<<"2. Billing"<<endl;
        cout<<"3. Sales"<<endl;
        cout<<"4. Exit"<<endl;
        cout<<"---------------------------------"<<endl;
        cout<<"Choose option: ";
        cin>>ch;
        switch(ch){
            case 1:
            {inventory i;
            break;}
            case 2:
            {billing b;
            b.Billing();
            break;}
            case 3:
            {sales::salesMenu();
            break;}
            case 4:
           { break;}
            default:
            {cout<<"Invalid choice!";}
        }
    }
    product::savetofile();
    sales::savetofile();
    return 0;
}