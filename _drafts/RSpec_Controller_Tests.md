Controller tests are slightly more complex than model tests bacause you have to operate within a request-response context.

The workflow is to call the request macro either in a setup `before` block or in the test itself.

```ruby
describe "GET show" do
  before :each do
    get(:show, { param1: "value1"}, format: :json)
  end

  it "should redirect to root if no user by email detected" do
    get(:show, { param2: "value2"}, format: :json)
  end

  # POST
  # post(:create, {param: "value"}, format: :html)

  # You can also use block notation to expect errors
  # expect{
  #   post(:create, {contact: Factory.attributes_for(:contact, :invalid)}
  # }.to raise_error(ErrorClass, /error_message_regex/)

  # Or to check changes
  # expect{
  #   post :create, contact: Factory.attributes_for(:contact)
  # }.to change(Contact, :count).by(1)
```

After calling the request macro you get a `response` object.  
You will be checking three things in the response context

### 1. Response status

```ruby
it "should return 200 status" do
  get :calendar, locale: "lv", email: "none@test.cz"
  expect(response.status).to eq 200
end
```

### 2. Response redirect

```ruby
it "should redirect to root path" do
  get :calendar, bad: "param"
  expect(response).to redirect_to(root_path)
end
```

### 3. Rendered template

```ruby
it "should render :calendar template" do
  get :calendar
  expect(response).to render_template(:calendar)
end
```

### 4. Response local variables (controller->view)

```ruby
it "should redirect to root path" do
  get :calendar, bad: "param"
  expect(assigns(:calendar).class).to eq(Calendar)
end
```

Consult [Rspec docs](https://www.relishapp.com/rspec/rspec-rails/docs/controller-specs) for more.

## Testing controller modules

### 1. Use Anonymous Controller

```ruby
RSpec.describe Namespace::SomeControllerModule, type: :controller do
  controller(CreativeSubscriptions::ApplicationController) do # here the passed argument can be omitted if inheritance is simple
    include Namespace::SomeControllerModule

    def custom_action
      render :text => "custom called"
    end
  end

  describe "GET :custom_action" do
    let(:route) { routes.draw { get "custom" => "creative_subscriptions/application#custom" } }    

    it "should work" do
      route
      get :custom_action
      expect(response.body).to eq "custom called"
      expect(response.status).to eq 200
    end
  end

  # describe "#catch_flash_message" do
  #   it "should set flash from get params :flash_info and :flash_notice" do
  #     subject.params = {flash_info: "info", flash_notice: "notice"}
  #     subject.send(:catch_flash_message)
  #     expect(flash[:info]).to eq "info"
  #     expect(flash[:notice]).to eq "notice"
  #   end
  # end
  #
  # describe "#set_locale_to_lv" do
  #   it "should set I18n to :lv" do
  #     subject.send(:set_locale_to_lv)
  #     expect(I18n.locale).to eq :lv
  #   end
  # end  

end

```



Consult [Pivotal Labs' post](https://blog.pivotal.io/labs/labs/testing-modules-that-get-included-in-a-controller)
