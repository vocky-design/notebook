BehaviorTree.CPP

BtEditor

###### 可以换一个程序吗，比如我想测试4个函数的所有参数的可能情况，向用BehaviorTree.CPP测一下这个，四个函数你可以自己定义

首先，我们可以使用BehaviorTree.CPP框架来定义一组测试行为树，该行为树将遍历所有可能的参数组合，并在每个组合上调用相应的函数进行测试。假设我们要测试以下四个函数：

```c++
int add(int a, int b);
int subtract(int a, int b);
float multiply(float a, float b);
float divide(float a, float b);
```

我们可以为每个函数定义一个行为节点，该节点将执行相应的函数调用，并将结果存储在共享黑板中，以供后续行为节点使用。

接下来，我们可以使用组合节点来组合这些行为节点，以实现遍历所有可能的参数组合。我们可以使用并行节点来并行执行所有行为节点，以减少测试时间。我们还可以使用条件节点来检查测试结果是否正确，并根据需要执行其他行为。

下面是一个使用BehaviorTree.CPP框架测试四个函数的示例行为树：

```c++
// Behavior tree to test four functions with all possible parameter combinations
using namespace BT;

// Define the behavior tree
BT::BehaviorTreeFactory factory;

factory.registerSimpleCondition("IsTestComplete", [](){ return test_complete; });

// Define the behavior nodes for each function
factory.registerSimpleAction("Add", std::bind(&add, blackboard, _1, _2));
factory.registerSimpleAction("Subtract", std::bind(&subtract, blackboard, _1, _2));
factory.registerSimpleAction("Multiply", std::bind(&multiply, blackboard, _1, _2));
factory.registerSimpleAction("Divide", std::bind(&divide, blackboard, _1, _2));

// Define the composite nodes to test all possible parameter combinations
factory.registerFallback("TestAllFunctions");
factory.registerSequence("TestParameters");
factory.registerParallel("TestAllParams", 4, 1);

// Define the parameters to test
const int num_params = 5;
int params[num_params] = {1, 2, 3, 4, 5};
float fparams[num_params] = {0.1f, 0.2f, 0.3f, 0.4f, 0.5f};

// Add the test behavior nodes to the tree
for (int i = 0; i < num_params; i++)
{
    for (int j = 0; j < num_params; j++)
    {
        factory.registerSimpleAction(
            "TestAdd" + std::to_string(params[i]) + "And" + std::to_string(params[j]),
            std::bind(&test_add, blackboard, params[i], params[j])
        );
        factory.registerSimpleAction(
            "TestSubtract" + std::to_string(params[i]) + "And" + std::to_string(params[j]),
            std::bind(&test_subtract, blackboard, params[i], params[j])
        );
        factory.registerSimpleAction(
            "TestMultiply" + std::to_string(fparams[i]) + "And" + std::to_string(fparams[j]),
            std::bind(&test_multiply, blackboard, fparams[i], fparams[j])
        );
        factory.registerSimpleAction(
            "TestDivide" + std::to_string(fparams[i]) + "And" + std::to_string(fparams[j]),
            std::bind(&test_divide, blackboard, fparams[i], fparams[j])
		);
	}
}

// Add the test behavior nodes to the composite nodes
for (int i = 0; i < num_params; i++)
{
    for (int j = 0; j < num_params; j++)
    {
        BT::NodeBuilder builder_test_add = [](const std::string& name, const BT::NodeConfiguration& config) {
        return std::make_unique<TestAdd>(name, config, params[i], params[j]);
    };   
        
    factory.registerBuilder<TestAdd>(
    "TestAdd" + std::to_string(params[i]) + "And" + std::to_string(params[j]), builder_test_add
    );
        
    BT::NodeBuilder builder_test_subtract = [](const std::string& name, const BT::NodeConfiguration& config) {
    return std::make_unique<TestSubtract>(name, config, params[i], params[j]);
    };  
        
    factory.registerBuilder<TestSubtract>(
        "TestSubtract" + std::to_string(params[i]) + "And" + std::to_string(params[j]), builder_test_subtract
    );
        
    BT::NodeBuilder builder_test_multiply = [](const std::string& name, const BT::NodeConfiguration& config) {
        return std::make_unique<TestMultiply>(name, config, fparams[i], fparams[j]);
    };
        
    factory.registerBuilder<TestMultiply>(
        "TestMultiply" + std::to_string(fparams[i]) + "And" + std::to_string(fparams[j]), builder_test_multiply
    );
    
    BT::NodeBuilder builder_test_divide = [](const std::string& name, const BT::NodeConfiguration& config) {
        return std::make_unique<TestDivide>(name, config, fparams[i], fparams[j]);
    };
        
    factory.registerBuilder<TestDivide>(
        "TestDivide" + std::to_string(fparams[i]) + "And" + std::to_string(fparams[j]), builder_test_divide
    );
    
    factory.registerFallback("TestParams" + std::to_string(params[i]) + "And" + std::to_string(params[j]));
    
    factory.registerSequence("TestAdd" + std::to_string(params[i]) + "And" + std::to_string(params[j]));
    factory.registerSequence("TestSubtract" + std::to_string(params[i]) + "And" + std::to_string(params[j]));
    factory.registerSequence("TestMultiply" + std::to_string(fparams[i]) + "And" + std::to_string(fparams[j]));
    factory.registerSequence("TestDivide" + std::to_string(fparams[i]) + "And" + std::to_string(fparams[j]));
}
    
// Build the tree
auto tree = factory.createTreeFromText(
R"(
<root>
<TestAllFunctions>
<TestAllParams>
<TestParams1And1/>
<TestParams1And2/>
<TestParams1And3/>
<TestParams1And4/>
<TestParams1And5/>
<TestParams2And1/>
<TestParams2And2/>
<TestParams2And3/>
<TestParams2And4/>
<TestParams2And5/>
<TestParams3And1/>
<TestParams3And2/>
<TestParams3And3/>
<TestParams3And4/>
<TestParams3And5/>
<TestParams4And1/>
<TestParams4And2/>
<TestParams4And3/>
<TestParams4And4/>
<TestParams4And5/>
</TestAllParams>
</TestAllFunctions>
)"
);

// Run the behavior tree
while (tree.rootNode()->executeTick() == NodeStatus::RUNNING)
{
// Do nothing
}

// Print the test results
std::cout << "Test results:\n";
std::cout << "Add: " << test_add_count << " passed, " << test_add_fail_count << " failed\n";
std::cout << "Subtract: " << test_subtract_count << " passed, " << test_subtract_fail_count << " failed\n";
std::cout << "Multiply: " << test_multiply_count << " passed, " << test_multiply_fail_count << " failed\n";
std::cout << "Divide: " << test_divide_count << " passed, " << test_divide_fail_count << " failed\n";
    
// Clean up memory
delete blackboard;

return 0;
}
```

