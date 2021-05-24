require 'byebug'
require 'rainbow'
require 'require_all'
require 'rake'

if p = ENV['TW_PATH']
  a = File.join(p, '/config/environment')
  if true # Dir.exists?(a)
    puts Rainbow("TaxonWorks found at #{a}").green
    require_relative a
    true
  else
    puts Rainbow("TaxonWorks NOT found at #{a}").red
    exit
  end 
else
  puts Rainbow("ENV variable 'TW_PATH' not set, do `TW_PATH=/path/to/taxonworks && export TW_PATH`.").red
  exit 
end

desc 'default'
task :default do
  puts Rainbow("Configuration successful.").purple
end

desc 'set all fossils to status extinct'
task :set_extinct do

  Current.project_id = Project.find_by(name: "Opiliones of the World").id
  Current.user_id = User.find_by(email: 'adrianok@gmail.com').id
  extinct_predicate = Predicate.with_project_id(Current.project_id).find_by(uri: 'https://api.catalogue.life/datapackage#Taxon.extinct')

  Otu.where(
    project: Current.project_id,
    taxon_name: TaxonName.where(
      taxon_name_classifications: TaxonNameClassification.where(
        TaxonNameClassification.arel_table[:type].matches('%fossil')
      )
    )
  ).each do |otu|
    InternalAttribute.create!(
      predicate: extinct_predicate,
      attribute_subject: otu,
      value: '1'
    )
  end
end
