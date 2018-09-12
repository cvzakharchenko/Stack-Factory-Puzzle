My solution. Not short, not elegant, but works.

```C++
#include <iostream>
#include <string>
#include <memory>
#include <cassert>


struct X
{
    friend struct X0;
    friend struct X1;
    friend struct X2;


    virtual ~X() {}


    virtual void Process() = 0;

private:
    X() {}
};


struct X0 final : public X
{
    X0() { std::cout << "X0::X0 "; }


    ~X0() { std::cout << "\tX0::~X0\n"; }


    void Process() override { std::cout << "X0::Process: " << c_; }


    char c_ = 'A';
};


struct X1 final : public X
{
    X1() { std::cout << "X1::X1 "; }


    ~X1() { std::cout << "\tX1::~X1\n"; }


    void Process() override { std::cout << "X1::Process: " << s_; }


    std::string s_ = "ABC";
};


struct X2 final : public X
{
    X2() { std::cout << "X2::X2 "; }


    ~X2() { std::cout << "\tX2::~X2\n"; }


    void Process() override { std::cout << "X2::Process: " << i_; }


    int i_ = 123;
};


int main()
{
    for (auto i = 0; i < 3; i++)
    {
        X* pX = nullptr;

        // Begin

        struct tag0 {};
        struct tag1 {};
        struct tag2 {};

        struct Test
        {
            X* x;

            union
            {
              X0 x0;
              X1 x1;
              X2 x2;
            };

            Test(tag0) : x0{}, x{&x0} { }
            Test(tag1) : x1{}, x{&x1} { }
            Test(tag2) : x2{}, x{&x2} { }

            ~Test()
            {
                x->~X();
            }

            static Test get(int x)
            {
                switch(x)
                {
                case 0:
                    return Test{tag0{}};
                case 1:
                    return Test{tag1{}};
                case 2:
                    return Test{tag2{}};
                };
            }

        };

        Test test = Test::get(i);
        pX = test.x;

        // End

        pX->Process();
    }

    return 0;
}
```
